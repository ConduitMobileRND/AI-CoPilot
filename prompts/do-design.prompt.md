---
mode: agent
---
You are an expert software architect and code reader. Your task is to produce a complete design document (Markdown) for the repository whose root is at {ROOT_PATH}. Produce a single Markdown file suitable for design.md and place it in docs folder at {ROOT_PATH}/docs. Be explicit, concise, and factual: extract facts from code where possible and clearly mark anything you infer as an assumption.

Context (fill these in or let the assistant infer):

repo_name: {REPO_NAME}
root_path: {ROOT_PATH}
key_files_to_scan: {FILES_TO_ANALYZE} # e.g. ["main.py", "app/init.py", "app/model.py", "app/loyalty.py", "app/smart_actions.py", "app/population.py", "app/crud.py", "app/utils.py", "requirements.txt", "README.md"]
optional: pass a short README or a note describing business goal (if available): {BUSINESS_GOAL}
Required output format (Markdown):

Title and short one-paragraph repo description (2-4 sentences).
Goals and constraints (1-3 bullets).
High-level architecture / components section:
short textual summary of components (component name + responsibility)
include a mermaid "graph TD" components diagram block (complete mermaid code).
Class diagrams:
Identify major classes/modules, for each list: name, public attributes, public methods, and brief responsibility (1 sentence).
Provide a mermaid classDiagram block covering these classes and their relationships (inheritance, composition, associations).
Sequence diagrams (for 3–5 major flows):
For each major flow, provide:
flow name (short)
textual summary (1-2 sentences)
mermaid sequenceDiagram block that shows participants and main messages (calls/returns/events).
Suggested flows to attempt (if present in code): "campaign creation or trigger", "data population / ingestion", "smart action decision/evaluation", "loyalty evaluation / scoring", "persistence / CRUD operations".
Data model summary:
Key data structures (classes, dict shapes), fields, and types.
Any database or persistence notes (table names, key fields) if derivable.
Non-functional considerations:
Observability, scaling, retries, security/permissions, performance risks.
Assumptions & open questions:
List each assumption you make when code lacks explicit info; add any open questions for maintainers.
Files referenced:
Include a short list of the files and (where possible) the line ranges or function/class names you used to extract the above.
How to validate:
Small checklist describing how to verify the architecture (smoke tests or quick manual checks).
Appendices (optional):
Small code excerpts (1–2 function/class examples) that illustrate key behavior.
If asked, also produce PlantUML versions of diagrams.
Formatting and behavior rules for the LLM:

Use mermaid syntax for all diagrams by default. Provide PlantUML alternatives when asked.
Use code fences for mermaid blocks:

%% mermaid diagram here
Be explicit when a relationship or behavior is inferred (prefix those lines with "Assume:").
Keep the generated document under ~1500–3000 words (unless asked to expand); prioritize clarity.
Make diagrams minimal but complete enough to guide implementation and reviews.
When listing class attributes/methods, include types if obvious from code; otherwise use "unknown".
If a required artifact (e.g., sequence of a flow) cannot be derived from code, provide a best-effort draft and mark it as "inferred / needs confirmation".
Add a short summary at top: "Changes recommended" (1–3 bullets with concrete next steps).
Step-by-step process to follow (for the LLM):

Scan the provided files in {REPO_PATH}. Extract top-level modules, classes, functions, and any entrypoints (CLI, main, or server startup).
Identify components by grouping related modules (e.g., API layer, domain services, persistence, decision engines, data population).
For each component produce a short responsibility statement and map out dependencies.
Produce mermaid diagrams (component, class, sequence) using exact names found in code.
Collect assumptions and open questions while generating the doc.
Output the final Markdown document matching the required format above to docs/design.md in the repo.
