# Coding Agent Benchmark Starter

Coding-agent scores answer different questions. A maintainer choosing an agent for repository work should first decide whether the target is **issue resolution**, **terminal operation**, **small function synthesis**, or **private project tasks**; only then is a leaderboard number meaningful.

This starter turns that distinction into three runnable pilot lanes and one reusable task packet. It is intentionally separate from an LLM application-regression suite: this repository grades an agent completing engineering work, while `llm-test-determinism-guide` controls repeated model/application checks in CI.

## Pick a lane

```text
Need patches for real repository issues?
└─ Issue lane: SWE-bench-style task + hidden tests + private replay tasks

Need shell/package/debugging work rather than only patches?
└─ Terminal lane: isolated task image + exact terminal-state oracle

Need adoption evidence for your own repository?
└─ Internal lane: sanitized historical task + project tests + human invariants
```

### Three starting combinations

| Lane | Public anchor | Execution unit | Oracle | Add before adoption |
|---|---|---|---|---|
| Repository issue | SWE-bench / SWE-bench Verified | One issue against a pinned repository snapshot | Tests supplied with the task | 5–10 sanitized issues from your own repository, including multi-file and documentation-sensitive work |
| Terminal work | Terminal-Bench or Harbor task images | One isolated shell task | Task-specific verifier and final filesystem/process state | Your package manager, build tooling, timeout policy, and forbidden-action checks |
| Internal engineering | Inspect AI or an in-house runner; OpenHands can be studied as an end-to-end harness | One frozen task packet | Public tests + hidden tests + human invariant card | Representative failure cost, cost/time budget, and tasks the agent has never seen |

HumanEval and Aider’s polyglot benchmark are useful **baselines**, not substitutes for these three lanes: the former isolates small Python functions; the latter measures editing under Aider’s workflow. Do not put either score in the same “winner” column as repository or terminal tasks.

## Task packet: the smallest useful unit

Copy [`assets/task-packet.example.yaml`](assets/task-packet.example.yaml) for each private task. Freeze:

- repository and base commit;
- user-visible request and allowed working directory;
- network, command, time, and cost policy;
- public tests, hidden-test command, and human invariants;
- forbidden effects such as secret access, force-push, or writes outside the workspace;
- success, partial-credit, timeout, and infrastructure-error outcomes.

A score without this packet is not reproducible enough for an adoption decision.

## Scoring without mixing unlike results

Report a lane as a vector, not one magic percentage:

1. **Task success** — hidden oracle passes.
2. **Safety** — no forbidden action or out-of-scope write.
3. **Patch quality** — human invariants hold; unnecessary churn is bounded.
4. **Efficiency** — wall time, tool calls, and cost under the frozen budget.
5. **Reproducibility** — reruns preserve task version, agent/model version, harness, and environment digest.

Public benchmark results may be contaminated or optimized against because tasks and scoring are public. That is an evaluation risk to test with private tasks—not a claim that a particular published score is contaminated.

## Pilot protocol

1. Select one lane and 10–20 tasks; do not combine lane scores.
2. Pin repository snapshots, images, harness commit, agent/model identifier, and configuration.
3. Run in disposable workspaces with no production credentials. Default-deny external network unless the task explicitly requires an allowlisted host.
4. Separate **agent failure** from **task/harness failure**. A broken image or flaky test must not count as model failure.
5. Repeat a small subset to expose variance. Preserve raw logs with secrets removed.
6. Review every apparent success against the human invariant card; tests alone may miss unauthorized behavior.
7. Add private tasks before procurement or broad rollout. Public leaderboards are discovery evidence, not local acceptance.

## Resource roles and evidence limits

| Resource | What its upstream supports | Appropriate use here | Do not infer |
|---|---|---|---|
| [SWE-bench](https://github.com/SWE-bench/SWE-bench) | Repository issues, snapshots, and tests | Issue-resolution baseline | Transfer to your private repository |
| [SWE-bench Verified](https://openai.com/index/introducing-swe-bench-verified/) | A human-validated subset of SWE-bench | A cleaner public issue subset | Immunity to public-task optimization |
| [OpenHands](https://github.com/All-Hands-AI/OpenHands) | End-to-end agent and evaluation integrations | Study a complete harness/runtime | Runtime-neutral scores |
| [Harbor](https://github.com/laude-institute/harbor) | Containerized agent-task execution and adapters | Unify isolated task execution | Correct task images or verifiers |
| [Terminal-Bench](https://github.com/laude-institute/terminal-bench) | Terminal-oriented task benchmark | CLI and environment work | Maintainable patch quality |
| [Aider benchmark](https://aider.chat/docs/leaderboards/) | Editing benchmark under Aider’s workflow | Editing baseline | General agent autonomy |
| [HumanEval](https://github.com/openai/human-eval) | Small Python functions graded by tests | Smoke baseline | Repository navigation ability |
| [Inspect AI](https://github.com/UKGovernmentBEIS/inspect_ai) | Datasets, solvers, tools, scorers, and logs | Author auditable internal tasks | A ready-made repository benchmark |

The capability descriptions above come from the linked project or official page reviewed on 2026-11-21. Lane composition, scoring vector, and pilot protocol are **this guide’s recommended design**, not claims made by every upstream project.

## Refresh policy

Recheck a row when its task format, evaluator, environment, license, or maintenance state changes. Add a resource only if it fills a missing task lane. Never refresh merely to change stars or dates. Keep private task text and hidden tests out of the public repository.

Structured resource metadata is in [`sources/sources.json`](sources/sources.json). The executable reader artifact is [`assets/task-packet.example.yaml`](assets/task-packet.example.yaml).

## License

Original guide text and the task-packet template are available under CC BY 4.0. Linked projects retain their own licenses and trademarks.
