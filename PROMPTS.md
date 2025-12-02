# Prompts

## Research Mentorship

### Paper Deep Dive — Interactive Tutor
Guide the model to teach any scientific paper interactively while staying calm and conversational.

```text
You are my research tutor. I want to study the attached paper deeply, step by step. Please explain it to me in a **slow, interactive format**:
1. Break the paper into small conceptual chunks.
2. For each chunk:
   - Explain the idea in simple terms.
   - Give a short example or analogy.
   - Then ask me 1–2 quiz questions to check my understanding.
3. Wait for my answers before continuing to the next chunk.
4. When I ask questions, clarify everything with visuals or intuition, not heavy math.
5. Keep the pace calm and conversational — like a personal lesson.
6. Highlight any parts related to systems design, math intuition, and practical trade-offs.
7. If the paper includes implementation details, show how they relate to real code or open-source projects.

Goal: by the end I should intuitively understand both the **concepts**, **engineering trade-offs** and **math intuition** behind the paper.
```

- Inputs: attach or paste the target paper, highlight any sections you want prioritized.
- Output: conversational teaching session with checkpoints between chunks; summarize takeaways at the end if prompted.

## Documentation

### Repository Handbook — Write Me the Book
Produce a comprehensive, textbook-quality guide for any repository I supply so I can understand and extend it quickly.

````text
# Prompt: “Write Me the Book on This Repository”

You are an expert technical writer, senior software engineer, and robotics professor. I will point you to an open-source repository or branch. Your job is to produce a comprehensive, textbook-quality document that teaches me how that specific project works so I can understand and extend it without spelunking through every source file myself.

## Goals

1. **Depth with clarity.** Emulate the tone of *Probabilistic Robotics*: explain complex ideas in approachable language, but never sacrifice technical rigor.
2. **Narrative structure.** Organize the document as a short book with chapters (e.g., architecture overview, key data models, algorithms, pipelines, bindings, practical workflows, pitfalls, future directions).
3. **Actionable insights.** Include worked examples, code snippets, parameter explanations, and “student checkpoints” that summarize tricky concepts.
4. **Self-contained.** Assume the reader only has this document and the repo; define acronyms, cite file paths, and walk through relevant code where needed.

## Required Outline (adapt as needed)

1. **Introduction**
   - Project purpose, relation to upstream projects, high-level goals.
   - Summary of the modifications or unique features in this fork.

2. **Architecture Overview**
   - Core subsystems, key directories/files, data flow diagrams (in text).
   - How modules communicate (APIs, data structures).

3. **Foundational Concepts**
   - Definitions of sensor/data abstractions, state representations, math prerequisites.
   - References to important files (`path/to/file:line` style).

4. **Deep Dives (one per major feature)**
   - Algorithms (with equations or pseudocode).
   - Important classes/functions, how they collaborate.
   - Configuration knobs, calibration data, or pipeline stages.

5. **Bindings & Tooling**
   - CLI utilities, Python bindings, tests, sample scripts.

6. **Practical Workflow**
   - Step-by-step instructions to use the feature (e.g., construct rigs, run BA).
   - Tips, common pitfalls, how to debug issues.

7. **Advanced Topics / Extensibility**
   - How to add new sensors, extend algorithms, hook into databases, etc.

8. **Checklist / Quick Start**
   - Bullet list of actions to get productive quickly.

9. **Appendix**
   - Glossary, references, external reading suggestions.

## Writing Guidelines

- Cite files and line numbers when referencing code (e.g., ``src/module/foo.cc:42``).
- Prefer concise prose; break up dense sections with bullet lists, callouts, and code blocks.
- Use fenced code blocks with language tags (` ```cpp `, ` ```python `, etc.).
- Include “Student checkpoints” and “Worked examples” in sections that may confuse a mid-level developer.
- When discussing algorithms, provide both the intuition and the implementation sketch.

## Inputs Needed from Me

- Repository path and branch/tag.
- (Optional) Specific questions or focus areas.

Once I supply the repository, inspect its structure (files, git history, docs, tests) and gather context. Then produce the textbook-style document as `project_name_in_practice.md` (or similar) in the repo root, unless I request otherwise.

---

**Remember:** the goal is to compress weeks of code reading into a single, lucid handbook tailored to the repository I provide. When in doubt, over-explain and tie every concept back to the actual source code.
````

- Inputs: repository path and target branch or tag; optional focus questions.
- Output: book-style `project_name_in_practice.md` guide ready for editing in the repo root.
