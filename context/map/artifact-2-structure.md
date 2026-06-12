# Artifact 2: Structure

This document analyzes the project's structure, focusing on package-level dependencies, shared contracts, entry points, and architectural cycles.

## 1. Contracts and Shared Layers Between Layers

Lombok is split into several distinct layers, and there are explicit shared contracts between them:

- **Core Shared Layer (`lombok.core.handlers`):**
    This package serves as a bridge between the compiler-agnostic core and the compiler-specific handlers. Both `lombok.eclipse.handlers` and `lombok.javac.handlers` depend on `lombok.core.handlers`. It contains utility classes and shared logic that handler implementations on both platforms use to generate boilerplate.

- **AST Core (`lombok.core`):**
    This package defines Lombok's core AST abstractions (`LombokNode`, `AST`). Both the Eclipse implementation (`lombok.eclipse`) and the Javac implementation (`lombok.javac`) depend heavily on `lombok.core` to build compiler-specific node representations and run handlers.

- **Configuration (`lombok.core.configuration`):**
    This package contains configuration-related logic (handling `lombok.config` files). It is used across almost all layers (core, javac handlers, eclipse handlers) to resolve settings like flag usage, access levels, etc.

## 2. Entry Points and Deeper Centers

- **Thin Entry Points:**
    - `lombok.launch`: The main entry point for starting Lombok. It handles loading the actual Lombok classes via a custom `ShadowClassLoader` to prevent namespace pollution. It has many outward dependencies but few internal things depend on it directly.
    - `lombok.javac.apt` (`LombokProcessor`): The entry point for Javac's annotation processing API. It initializes the Javac compiler integration and delegates to `lombok.javac` and handlers.
    - `lombok.installer`: The entry point for the GUI installer. It has dependency connections to OS-specific elements and is mostly decoupled from the compiler handlers.

- **Deeper Centers (High Blast Radius):**
    - `lombok.core`: The deepest center. Almost all packages depend on it. Any change here has a massive blast radius.
    - `lombok.core.configuration`: Highly connected, used by core and all handler packages.
    - `lombok.javac.handlers` and `lombok.eclipse.handlers`: Deeper centers for compiler-specific behavior. They contain all annotation-specific logic.

## 3. Architectural Cycles and Suspicious Dependencies

The package-dependency graph reveals several tight coupling cycles:

- **Core-Configuration Cycle:**
    - `lombok.core` depends on `lombok.core.configuration`.
    - `lombok.core.configuration` depends on `lombok.core`.
    This direct cycle is a tight coupling that indicates configuration and core logic are interdependent.

- **Compiler-specific Handler Cycles:**
    - `lombok.javac` depends on `lombok.javac.handlers`.
    - `lombok.javac.handlers` depends on `lombok.javac`.
    - `lombok.eclipse.handlers` depends on `lombok.eclipse.agent`.
    - `lombok.eclipse.agent` depends on `lombok.eclipse.handlers`.
    These cycles suggest that compiler integrations and their handlers are highly coupled, making it difficult to change one without affecting the other.

## 4. Blast Radius (Impact of Changes)

- **Changing `lombok.core`:**
    - **Impact:** Critical.
    - **Blast Radius:** 100% of Lombok. Any modification to AST logic or node definitions will affect every single handler (javac and eclipse) and the launch/loading mechanism.

- **Changing `lombok.core.handlers`:**
    - **Impact:** High.
    - **Blast Radius:** Large. Any changes here will affect both Javac and Eclipse handler implementations. For example, changing a helper method for generating setters could break setter generation on both compilers.

- **Changing `lombok.javac.handlers` / `lombok.eclipse.handlers`:**
    - **Impact:** Medium.
    - **Blast Radius:** Isolated to the specific compiler. A change in `lombok.javac.handlers` will not break Eclipse support, and vice-versa. This is the safest area to make compiler-specific changes.
