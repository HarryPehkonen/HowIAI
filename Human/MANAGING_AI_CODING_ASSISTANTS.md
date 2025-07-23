# Managing AI Coding Assistants: The Dual-AI Strategy

## Overview

AI coding assistants are powerful tools for implementation but lack the global context and architectural judgment of a senior developer. This document outlines the "Dual-AI Strategy," a process for leveraging two distinct AI roles—the **Specialist** and the **Architect**—to maximize productivity while maintaining high code quality and architectural integrity.

This strategy was developed during the Computo project, where Claude was used as the Specialist and Gemini served as the Architect, with a human lead guiding the entire process.

## The Core Problem

A single AI assistant, when used for both coding and review, tends to:
- **Over-engineer solutions** based on textbook patterns without considering project context.
- **Introduce inconsistencies** by solving similar problems in different ways across the codebase.
- **Lack the ability to question a prompt's premise**, leading to the implementation of unnecessary features.
- **Fail to see opportunities for abstraction** across different files or modules.

## The Dual-AI Roles

The solution is to treat AIs as specialized team members with distinct roles.

### 1. The Specialist AI (The Pair Programmer)
*   **Example Tool:** Claude
*   **Role:** Focused on **local context** and **implementation**. This AI is your tireless pair programmer.
*   **Strengths:**
    -   Writing boilerplate code.
    -   Implementing well-defined algorithms.
    -   Generating unit tests from a function signature.
    -   Refactoring a single function or class.
    -   Explaining a specific block of code.
*   **Weaknesses:**
    -   Architectural vision.
    -   Codebase-wide consistency.
    -   Identifying anti-patterns it just created.
    -   Recognizing when a simpler solution is better.

### 2. The Architect AI (The Gatekeeper)
*   **Example Tool:** Gemini
*   **Role:** Focused on **global context** and **architectural review**. This AI is your periodic code reviewer with a whole-project view.
*   **Strengths:**
    -   Ingesting and analyzing large amounts of code at once.
    -   Identifying code smells, anti-patterns, and code duplication.
    -   Spotting inconsistencies in design principles (e.g., n-ary operators).
    -   Suggesting high-level refactoring and abstractions.
*   **Weaknesses:**
    -   Can be slow or expensive for frequent use.
    -   May hallucinate or give generic, non-actionable advice without a specific prompt.
    -   Less effective for line-by-line implementation.

### 3. The Human Lead (The Architect & Final Authority)
*   **Role:** You are the project lead. The AIs are your tools.
*   **Responsibilities:**
    -   Define the architecture, principles, and requirements (e.g., `CORE_REQUIREMENTS.md`, `CLEAN_ARCHITECTURE.md`).
    -   Write clear, specific prompts for the Specialist AI.
    -   Make the final judgment on all code.
    -   Critically evaluate the Architect AI's feedback, discarding irrelevant suggestions.
    -   **Own the final code.**

## The Workflow

This workflow integrates the AI roles into a feature development cycle.

**Phase 1: Human-Led Definition**
1.  **Define the Task:** The human lead writes a clear specification for the feature or fix. This includes expected behavior, function signatures, and success criteria.
2.  **Write the Tests First (TDD):** The human writes the failing unit tests that the new code must pass.

**Phase 2: Specialist AI Implementation**
3.  **Prompt the Specialist:** The human works with the Specialist AI (e.g., Claude) to write the implementation.
    -   Provide the function signature and the failing tests.
    -   Prompt: `"Here is the header file and the failing Google Test cases. Please provide the C++ implementation for this function that passes these tests and adheres to our project's style."`
4.  **Integrate and Verify:** The human integrates the generated code, ensures it compiles without warnings, and verifies that all local tests pass.

**Phase 3: Architect AI Gatekeeping (Crucial Step)**
5.  **Prepare the Review Package:** Before committing, the human gathers the new/modified files and related existing files. For significant changes, **provide the entire codebase**.
6.  **Prompt the Architect:** The human submits the code to the Architect AI (e.g., Gemini) with a carefully crafted prompt.

    ---
    **Architect AI Prompt Template:**

    `You are an expert C++ software architect acting as a code reviewer for my project, "Computo". Your primary role is to identify high-level architectural issues, inconsistencies, and over-engineering. Do not focus on minor style issues.`

    `Here are our core architectural principles:`
    `1. Clean Library/CLI Separation: The core library has zero debugging overhead.`
    `2. JSON-Native Syntax: All scripts are simple JSON arrays.`
    `3. N-ary Operator Consistency: All operators should be n-ary where possible.`
    `4. Thread Safety by Design: We use immutable contexts and pure functions.`
    `5. Simplicity over Complexity: Avoid unnecessary abstractions like builders or memory pools.`

    `Based on these principles and general software engineering best practices, please review the following codebase. Specifically, look for:`
    `- **Signs of Over-Engineering:** Are there complex patterns where a simpler solution would suffice?`
    `- **Code Duplication:** Are there opportunities to abstract repeated logic into shared utilities?`
    `- **Inconsistencies:** Does the new code violate our established patterns (e.g., n-ary operators, error handling)?`
    `- **Violations of Our Principles:** Does any code contradict the five principles listed above?`
    `- **Potential Performance Bottlenecks:** Are there any obvious performance issues?`

    `Please provide your feedback in a concise, actionable list.`

    `[PASTE FULL CODEBASE HERE]`
    ---

**Phase 4: Human-Led Refactoring & Finalization**
7.  **Analyze Feedback:** The human lead critically reviews the Architect AI's suggestions. Some will be brilliant; others may be irrelevant. **This is where human judgment is irreplaceable.**
8.  **Refactor if Necessary:** If the feedback is valid, the human goes back to the Specialist AI with a new, specific prompt for refactoring.
    -   Prompt: `"The architectural review found that this function is duplicating logic from 'src/operators/shared.cpp'. Please refactor this code to use the existing 'evaluate_lambda' helper function instead."`
9.  **Final Commit:** Once the code is implemented, tested, and has passed architectural review, the human commits it.

## Conclusion

The Dual-AI Strategy is not about letting AIs write code autonomously. It is a structured process that empowers a human developer to use different AI tools for what they do best.

-   The **Specialist AI** accelerates implementation.
-   The **Architect AI** provides a crucial, automated "second opinion" that catches the high-level mistakes AIs and humans often make under pressure.
-   The **Human Lead** remains in full control, guiding the process and making the critical decisions that ensure the final product is clean, maintainable, and well-architected.

By adopting this strategy, development teams can achieve a powerful synergy: the speed of AI-driven implementation combined with the wisdom of human-led (and AI-assisted) architectural oversight.

