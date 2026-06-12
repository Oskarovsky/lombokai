# Artifact 1: Territory

This document analyzes the git history of the project to identify active and sensitive areas.

## 1. Consistently Active Directories and Files (Last 3 Months)

The following files and directories have been the most active in the last 3 months:

**Most Active Files:**
- `doc/changelog.markdown` (8 changes)
- `src/core/lombok/eclipse/handlers/HandleJacksonized.java` (6 changes)
- `src/core/lombok/javac/handlers/HandleJacksonized.java` (5 changes)
- `test/transform/resource/before/JacksonizedBuilderFluent.java` (3 changes)

**Most Active Directories:**
- `doc/`
- `src/core/lombok/eclipse/handlers/`
- `src/core/lombok/javac/handlers/`
- `test/transform/resource/before/`
- `test/transform/resource/after-ecj/`
- `test/transform/resource/after-delombok/`
- `buildScripts/`
- `.github/workflows/`

## 2. Files and Directories That Often Change Together

Based on the commit history, the following groups of files and directories often change together:

- **Jacksonized Feature:**
    - `src/core/lombok/eclipse/handlers/HandleJacksonized.java`
    - `src/core/lombok/javac/handlers/HandleJacksonized.java`
    - `test/transform/resource/before/JacksonizedBuilderFluent.java`
    - `test/transform/resource/after-ecj/JacksonizedBuilderFluent.java`
    - `test/transform/resource/after-delombok/JacksonizedBuilderFluent.java`

- **Build System:**
    - `buildScripts/tests.ant.xml`
    - `buildScripts/ivy.xml`
    - `.github/workflows/ant.yml`

## 3. Intersection of Activity with Key Areas

The activity in the last 3 months intersects with the following key areas:

- **Build:** Changes in `buildScripts/` and `.github/workflows/` directly affect the build process.
- **Integrations:** The frequent changes to `HandleJacksonized.java` indicate active development on the integration with the Jackson library.
- **Public API:** The `doc/changelog.markdown` is updated frequently, which suggests that the public API or behavior of the project is changing.
- **Runtime:** The file `src/launch/lombok/launch/ShadowClassLoader.java` is related to the project's runtime and has been modified.

## 4. Noise Filtering

The following observations are made regarding potential noise in the analysis:

- **Generated Files:** The files in `test/transform/resource/after-ecj/` and `test/transform/resource/after-delombok/` appear to be generated as part of the build or test process. While they are "noise" in terms of direct source code changes, they are valuable for understanding the impact of the changes in the source code.
- **No Obvious Noise:** There are no obvious noise files like lockfiles or snapshots in the list of frequently changed files.
