# Artifact 3: Contributor Context for `src/core`

This document analyzes the contributor activity for the `src/core` directory, which was identified as a central and critical part of the Lombok project.

## 1. Top Contributors (Last 12 Months)

The following contributors have been most active in `src/core` over the past year:

1.  **Reinier Zwitserloot** (24 commits) - Project Lead
2.  **Liam Pace** (11 commits)
3.  **Lars Uffmann** (9 commits)
4.  **Rafa** (5 commits)
5.  **Rawi01** (4 commits)

## 2. Contributor-Specific Topics

An analysis of commit messages reveals the following areas of focus for the top contributors:

-   **Reinier Zwitserloot (Project Lead):**
    -   **Releases & Versioning:** Handles version bumps and release preparation.
    -   **JDK Compatibility:** Ensures Lombok works with new JDK versions, fixing compatibility issues as they arise.
    -   **Core Infrastructure & Bug Fixes:** Works on the build system, configuration, and critical bug fixes, often merging PRs from other contributors.

-   **Liam Pace:**
    -   **Annotation Handlers:** Focuses on implementing and refactoring handlers for both Eclipse and Javac, with a particular emphasis on logging annotations (`@Log`, `@Slf4j`, etc.).
    -   **Jackson Integration:** Has contributed to the integration with the Jackson library.

-   **Lars Uffmann:**
    -   **`@Jacksonized` Feature:** His work is heavily concentrated on the `@Jacksonized` annotation, including adding support for Jackson 3 and improving the configuration around it.

## 3. Key PRs, Decisions, and Edge Cases

Before making changes to `src/core`, the following resources are worth reviewing:

-   **PR #3950: Support for Jackson 3 in `@Jacksonized`**: This pull request is a great case study for understanding how to add support for new versions of external libraries.
-   **`doc/changelog.markdown`**: This file is a rich source of information about bug fixes, new features, and the edge cases that were considered. For example, it documents tricky interactions between annotations like `@Setter`, `@Data`, and `@JsonProperty`.
-   **JDK Compatibility Commits:** The commit history from Reinier Zwitserloot highlights the importance of testing any changes against a wide range of JDK versions. The project has specific tests for different JDK versions, and any change in `src/core` should be validated against them.

## 4. Knowledge Distribution

The knowledge about `src/core` appears to be **both concentrated and distributed**:

-   **Concentrated Knowledge:** The project lead, **Reinier Zwitserloot**, holds a deep and broad understanding of the core architecture, release process, and JDK compatibility. His role is central to the project's stability.
-   **Distributed Knowledge:** Knowledge about specific features is well-distributed among other contributors. For example, **Lars Uffmann** is the expert on `@Jacksonized`, while **Liam Pace** has a strong understanding of the logging annotation handlers.

This model allows for a stable core maintained by the project lead, with a healthy ecosystem of contributors who own and develop specific features.
