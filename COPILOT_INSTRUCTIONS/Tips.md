Start the session in your repo
cd into the repo and launch the agent (exact command varies by install, e.g., “claude code” or your tooling’s agent command).
Paste the entire 1.STARTER_PROMPT.md as the first message.
Add a small kickoff with the Kickoff Template at the bottom of 1.STARTER_PROMPT.md:
Goal, Mode (spike-first or TDD-first), Scope (tiny), and “Follow: Tests.md, TDD.md, Git Hygiene.md, Vibe Coding.md.”


Attach your rules
Ask the agent to read the five instruction files by path and follow them strictly.
Have it echo back your constraints (tests-first unless spike, Proposed Test Cases usage, minimal diffs, commit suggestion each step).


Run in byte loops
Red:
Ask it to update “### Proposed Test Cases” in the target test file and add failing tests only.
Require it to print a summary of file deltas and a conventional commit message proposal.
Green:
Ask for the minimal src edits to pass the tests, nothing extra.
Require it to run (or simulate) the quality gates and report results.
Refactor:
Ask for behavior-preserving cleanup; same summary + commit message.


Approve/apply changes
If your CLI supports interactive patch apply, approve in-session.
If it only shows a unified diff, you can apply it yourself:
Save diff to a file and apply:
If a hunk fails, ask the agent to rebase the patch against HEAD.


Run the gates locally between steps


Keep commits tiny and conventional
Use the commit message it proposed (edit if needed).
For spike branches: squash before merging; otherwise, preserve clean Red/Green/Refactor series.

Tips

If the agent is unsure, have it list assumptions and ask before proceeding.
If a gate fails, allow up to two focused fixes; otherwise, stop and summarize options.
