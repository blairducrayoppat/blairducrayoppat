# Blair DuCray-Oppat, AIGP

I spent years in data privacy, finishing as Manager of Data Privacy and Data Governance at a public company. A lot of that time went to two questions, asked on behalf of people who would never see the systems that held their data: what can reach it, and what did it do once it got there. What I build now is for those same people: AI they can use that is trustworthy, safe, private, local, sustainable, free, and accessible to everyone, and I think the two questions are how you get there.

On September 20, 2026, a training agent inside OpenAI's sandbox [reached an outside chatbot through the DNS resolver](https://alignment.openai.com/misalignment-reports/an-agent-used-dns-to-reach-an-external-chatbot/). The web proxy had blocked it, so it encoded its questions as domain lookups and read the answers back in DNS records. A monitor flagged it within about fifteen minutes; the run kept going for two and a half hours more, because it did not stop on its own. OpenAI's fix was two independent blocking layers, either of which would have been enough. I read that the way I've read incidents for years: not whether the model was clever, but what it could reach and whether we can show what it did. An agent does not go through the wall you built; it goes through the seam you assumed was too small to matter, and a detector that notices is not a boundary that holds. Luke Hinds put the underlying rule in one line in his [sandbox primer](https://nolabs.ai/blog/sandbox-primer): "a prompt-level rule can be argued with; a kernel cannot."

Since November 2025 I've been building two things in my home lab. **BlarAI** is the private part: a local, security-first AI system on one Windows laptop, where every query and every document stays on hardware I control. **The Governator** is the containment part: a Windows app I am building to start any AI agent with the least it needs and let you give or take back what it can touch while it runs: files, network, tools and MCP servers, down to a single folder, port or tool call. I believe the laptop already in your bag can run useful AI privately, and that an AI agent acting on any machine should answer to controls you can check.

---

### BlarAI

The public snapshot of the code is [blarai-public](https://github.com/blairducrayoppat/blarai-public).

It runs on an Intel Core Ultra 7 258V (Lunar Lake) with 32 GB of memory and an Arc 140V GPU: no cloud, no telemetry, and zero external network dependency by default.

**How the controls are built**

- **Every decision leaves a signed record.** Each ruling the Policy Agent makes is written to an append-only log, hash-chained to the one before it and signed, with the action, the decision, the rule that fired, and whether I approved it. Content never goes in the record, only identifiers. Nothing is ever deleted; the whole chain can be verified from the first record.
- **Deterministic checks.** In the assistant, every tool call and fetch a model initiates is reduced to a canonical action and checked against deterministic rules.
- **Untrusted content is action-locked.** Outside content is datamarked when it comes in and action-locked: it can steer the model's words, but it cannot fire a non-exempt tool.
- **One network door in the runtime.** Exactly one module in the assistant runtime may import an HTTP client, enforced by an import scan. Optional web search goes out through that one deny-by-default door.
- **Hardware-rooted trust.** Non-exportable TPM 2.0 keys, signature-verified model manifests enforced fail-closed, and AES-256-GCM encryption at rest under a TPM-sealed key.
- **Tested off.** The standing rule is that a security control ships with a test that it blocks and a test that the probe fails when the control is off, so "secure" can be told apart from "the test can't reach it."

**Containing the coding agent.** BlarAI's coding agent writes and tests code with nobody typing, so what it can reach matters more than what it is told. I moved that step into a restricted Windows account, so that the account's permissions, not the agent's prompts or the dispatch scripts, decide what it can read and write, and a network block keyed to the account decides what it can reach. Then I measured it: the block held through a two-hour hold under a third-party security suite with every probe refused, survived a restart, and let the account reach the local model over loopback while every outside connection was refused at the socket. I compared it against the alternatives on the same machine: AppContainer, Microsoft's execution container, and the Windows backend of Anthropic's sandbox runtime. Every measurement, with its harness, raw samples and the list of what was not tested, is in [windows-containment-evidence](https://github.com/blairducrayoppat/windows-containment-evidence), published so anyone can re-run it and cite it at a fixed commit. In BlarAI the account is built and the switch is still off; flipping it is a ceremony I have not yet run. The measurements already have a second use: they are the ground the Governator is being designed on.


---

### The Governator

A second project, started in September 2026, and still being worked out. It starts from the same place BlarAI does: every decision about what an agent may do is written to a signed, hash-chained record before anything else happens. The Governator is designed to keep that record for any AI agent on a Windows PC, not just mine. It launches the agent in a Microsoft Execution Container or under a separate restricted Windows account, the two routes I have been measuring, and from then on every grant, every refusal and every change is in the chain.

The payoff of keeping that record is that nothing has to fail silently. When a policy blocks an action, the Governator already knows which policy, which session and which action, so it can tell the agent which policy stopped it and where its user can change that. The person hears about it from the AI they are already talking to, with the fix one click away, instead of staring at a tool that quietly stopped working.

The Governator runs as you; the agent runs in a separate, lesser identity it cannot climb out of, so it can never change a policy, only report that one stopped it. The human at the keyboard makes every change, from one place: network, files, tools and MCP servers, down to a single folder, port or tool call, switched on or off while the session runs. The app is meant to be navigated by anyone who can use a computer, with ready-made configurations for the common AI tools, and a built-in assistant that can read the setup but not change it, there to walk a person through getting what they want. It is meant for every Windows user, from a developer who wants precise control to someone who has never trusted an AI agent on their machine and would like to.

Its [decision record](https://github.com/blairducrayoppat/blarai-public/blob/619de306bbfb6db0ce80dfb0d51fb79490aa6fad/docs/adrs/ADR-049-Governator-Venture-Reopened.md) lives in BlarAI's repository. The code is not public or licensed yet; I intend to open-source it.

---

### Open source

I contribute to the tools I depend on: OpenVINO, OpenVINO GenAI, the NPU compiler, Optimum Intel and OpenVINO Model Server. Most of it is testing and measurement on the hardware in front of me, an Intel Lunar Lake laptop with an Arc 140V GPU and an NPU. When someone reports a bug they cannot pin down, I reproduce it here and post what I see. When a pull request needs a test run on this silicon, I run it. One fix of mine is merged upstream in OpenVINO GenAI. The open work is on GPU mixture-of-experts offload, NPU quantization and speculative decoding, and the full list is on GitHub: [pull requests](https://github.com/search?q=author%3Ablairducrayoppat&type=pullrequests), [issues](https://github.com/search?q=author%3Ablairducrayoppat&type=issues).

Every measurement I publish comes with the harness that produced it, the raw samples and a list of what was not tested, so anyone can run it again on their own machine. Two CC-BY-4.0 datasets on [Hugging Face](https://huggingface.co/blairducrayoppat) cover local inference on this laptop. The containment measurements are in [windows-containment-evidence](https://github.com/blairducrayoppat/windows-containment-evidence), one dated folder per result.

---

### How I build

I own the architecture, the acceptance criteria and the risk decisions; AI agents write all of the code. I had intermediate Java in college. A year ago I could not have written a Python program.

Say a bug in an upstream runtime is blocking my own work. I give Claude the goal and the bounds, then open a second session and have it check the first one's plan. While the two of them argue it out I read along in the terminal and learn more about the problem than I knew an hour earlier. When the fix is ready, Claude tells me what I should see when I run it. I run it. If I see that, we are done; if I don't, we are not. I don't need to understand every line of code for this to work, only how much I don't know, so I can bring that down to a level I can live with and keep the agent inside bounds it can't talk its way out of.

When the fix goes upstream, the pull request says it was drafted with AI assistance and that I approved it. If a maintainer asks a deep technical question, the reply is Claude's draft too, and it says so, because rewriting it in my own words would only disguise whose analysis it was. I don't know whose sentence that makes it. I don't think anyone does yet. So I say exactly what happened, every time, and let the work be judged on that.

Same bar at home, but there is more to see. Upstream I am mostly fixing something inside a runtime; in BlarAI I am shipping a feature I will use myself, so I can sit down and use it: hand the assistant a photo and check that its description matches what I see. Nothing merges until I have done that myself, and because the agents build faster than I can test, finished code has sat on branches for months waiting for me. When I hand BlarAI a job for the coding fleet, it breaks the job into tasks and gives me the acceptance criteria for each one before anything runs. If a criterion is weak I push back and have it strengthened; if the job is wrong I throw it out and start again. The test that enforces those criteria is written by BlarAI's model before any code exists, so it cannot be shaped to fit the code, and it is refused if it already passes on the skeleton before the feature is written. When a measurement comes back I look at the numbers before I look at the conclusion: are they in the range I expected, is a row missing, did we measure the thing we meant to with an instrument that can see it, and did we write down what else was running on the machine. Anything that would change the meaning of a result has to be on the record, or the result isn't one.

The rest the process does without me. A session reads the state on disk before it acts and says the task back to me before it starts. A hook refuses commits to main. Every merge runs the full test gate before and after, with the count predicted first so a wrong figure can't hide. A second agent that didn't write the code reviews it, and each thing it finds is fixed, parked against something a later session can check, or rejected in writing. Decisions and measurements are written down the day they happen, in files that get superseded, never edited.

The toolchain: **Claude Code** for interactive sessions, a coding fleet on a local model (OpenCode driving a 30B coder on the Arc 140V, [agentic-setup](https://github.com/blairducrayoppat/agentic-setup-public)) for autonomous runs, and a separate Claude session, which did none of the work, for review. The standing test gate runs 14,000+ tests at zero failures.

---

### Governance

I hold the IAPP's AIGP (AI Governance Professional) certification. I studied for it from my own incident log: 126 engineering failures from building BlarAI, mapped to the NIST AI Risk Management Framework, ISO/IEC 42001, the OECD AI Principles and EU AI Act concepts. That is a mapping, not a certification of the system. The audit chain is the kind of evidence those frameworks ask for: who decided what, under which rule, with the record intact.

---

### Connect

[![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/blair-do) [![Hugging Face](https://img.shields.io/badge/Hugging%20Face-yellow?logo=huggingface&logoColor=white)](https://huggingface.co/blairducrayoppat)
