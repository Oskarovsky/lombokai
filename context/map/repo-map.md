# Project Map: Lombok

This document synthesizes the analysis of Lombok's codebase, git history, and contributor activity to provide a strategic, decision-focused guide for developers and maintainers before applying major changes.

---

## 1. Active Territory (Recent Context)

Git analysis of the last 3 months indicates that while Lombok is a mature codebase, development is highly focused on a few modern features and platform updates:

- **Primary Feature Development:** The `@Jacksonized` annotation has been the most active feature. Handlers under `src/core/lombok/eclipse/handlers/HandleJacksonized.java` and `src/core/lombok/javac/handlers/HandleJacksonized.java` are updated frequently.
- **Platform & Build Maintenance:** Steady updates in `buildScripts/` (particularly `tests.ant.xml`) and `.github/workflows/ant.yml` show continuous integration adjustments for newer JDK versions.
- **Documentation:** Frequent updates to `doc/changelog.markdown` indicate that bug fixes and behavior changes are actively documented.

---

## 2. Architecture & Dependency Map

Lombok's internal packages are structured into distinct compilation and generation phases. The key package-level dependencies reveal a layered architecture with notable tight-coupling cycles:

```
                  +----------------------+
                  |    lombok.launch     |  <-- Entry Point (ShadowClassLoader)
                  +----------+-----------+
                             |
                             v
                  +----------------------+
                  |     lombok.core      |  <-- AST & Core abstractions
                  +----------+-----------+
                             ^
                             |  (Shared utility/bridges)
                  +----------+-----------+
                  | lombok.core.handlers |
                  +----------+-----------+
                    /                  \
                   /                    \
  +---------------+                      +---------------+
  |  lombok.javac |                      |lombok.eclipse | <-- Compiler Layers
  +-------+-------+                      +-------+-------+
          |                                      |
          v                                      v
  +---------------+                      +---------------+
  | lombok.javac. |                      |lombok.eclipse.| <-- Handler Layers
  |   handlers    |                      |   handlers    |
  +---------------+                      +---------------+
```

### Key Shared Layers:
- **`lombok.core` (AST Core):** Defines `LombokNode` and `AST` abstractions. Almost all internal packages depend on this.
- **`lombok.core.handlers` (Shared Handlers):** Houses core utility methods shared by both compilers' handlers (e.g. generating getter/setter logic at an abstract level).
- **`lombok.core.configuration` (Config Engine):** Used across the entire project to resolve `lombok.config` rules at compile time.

### Architectural Cycles (Risks):
- **Core-Configuration Cycle:** `lombok.core` and `lombok.core.configuration` depend on each other.
- **Compiler-specific Handler Cycles:** `lombok.javac <-> lombok.javac.handlers` and `lombok.eclipse.handlers <-> lombok.eclipse.agent`. These cycles indicate tight coupling between the AST parser wrappers and the boilerplate code generation handlers.

---

## 3. Blast Radius of Changes

Understanding the dependency graph allows us to categorize risk zones when making code changes:

| Risk Level | Target Area | Affected Packages / Behavior | Description |
| :--- | :--- | :--- | :--- |
| **Critical** | `lombok.core` | 100% of project, AST parsers | Any changes to core node structures or AST traversal can break both Eclipse and Javac builds. |
| **High** | `lombok.core.handlers` | Both Javac and Eclipse generation | A bug introduced here breaks boilerplate generation on both platforms simultaneously. |
| **Medium** | `lombok.javac.handlers` or `lombok.eclipse.handlers` | Only Javac or Only Eclipse | Platform-specific handlers are isolated. Changes to `lombok.javac` will not break Eclipse integration, and vice-versa. |
| **Low** | `lombok.installer`, `lombok.launch` | Boot, IDE integration | Changes to installer and loader modules might affect how Lombok loads, but won't alter compilation logic. |

---

## 4. Key Contributors & Knowledge Domain

The knowledge distribution in this repository is heavily concentrated around a few experts:

- **Reinier Zwitserloot (Project Lead):** Holds critical knowledge on **JDK Compatibility** (Javac 26, JVM internals), **Releases & Versioning**, and overall architecture. Over 50% of core commits in the last year are authored by him.
- **Lars Uffmann:** Domain expert for **Jackson integration**. Led the development of `@Jacksonized` improvements, configuration keys for Jackson versions, and the support for Jackson 3 (PR #3950).
- **Liam Pace:** Domain expert for **annotation handler refactoring** (particularly logging annotations and `@Log`/`@Slf4j` attributes).

---

## 5. Strategic Takeaways Before Codebase Modifications

1. **Strict Compiler Testing Required:** Because Javac changes its AST behavior across versions (e.g. `var` declarations in Javac 26), changes to core handlers must be tested against multiple JDKs.
2. **Review AST Interaction:** Before creating a new handler or modifying an existing one, read the files under `lombok.core.handlers` to check if a helper method already exists. Duplicate generation code is highly discouraged.
3. **Respect Shadowing Constraints:** Be careful when importing external packages. Lombok's `ShadowClassLoader` hides most internal classes from the user's project namespace, so dependencies must be carefully managed and registered in the `build.xml` file.
