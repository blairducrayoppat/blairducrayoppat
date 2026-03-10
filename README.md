# Hey, I'm Blair 👋

**Privacy Program Manager** by profession. **Local-AI researcher** by obsession.

My career protecting user privacy taught me something uncomfortable: the AI tools we rely on daily are architecturally incompatible with meaningful data sovereignty. Every query, every document, every personal thought fed into a cloud assistant traverses infrastructure you don't control, governed by policies you didn't write.

So I'm building the alternative.

---

### 🔒 BlarAI — A Decades-Long Local AI Platform

**BlarAI** is my primary research project: a security-first, multi-agent AI system designed to run *entirely* on a single consumer laptop — no cloud, no telemetry, no external network calls. Ever.

The system runs on an Intel Core Ultra 7 258V (Lunar Lake) with 32 GB LPDDR5X and an Arc 140V GPU, and includes:

- **Policy Agent** — A hybrid deterministic + probabilistic security gatekeeper that intercepts and classifies every inter-agent action before execution. Fail-closed by default.
- **Private Assistant** — A conversational AI with streaming TUI, modular skill routing, and a four-layer defense-in-depth against indirect prompt injection.
- **Personal Knowledge Substrate** — Semantically indexed local knowledge base with isochronous timing enforcement to eliminate retrieval side-channels.
- **Local Code Agent** — GPU-accelerated code generation producing atomic Git diffs with compile-test-oracle gate loops.

The architecture enforces hardware-rooted trust (Pluton measured boot), cryptographic action authorization (single-use Agentic JWTs with 5-second TTL), and mTLS over Hyper-V vsock IPC — ensuring that no single-layer failure can authorize a malicious action.

Built with [OpenVINO](https://github.com/openvinotoolkit/openvino) · [OpenVINO GenAI](https://github.com/openvinotoolkit/openvino.genai) · [Python](https://github.com/python/cpython) · [Qwen3](https://github.com/QwenLM/Qwen3) · [Intel Arc GPU](https://github.com/intel/compute-runtime) · [Intel NPU](https://github.com/openvinotoolkit/npu_compiler)

---

### 🛠️ Open Source Contributions

I contribute upstream fixes to the tools I depend on:

- **[openvinotoolkit/npu_compiler#265](https://github.com/openvinotoolkit/npu_compiler/pull/265)** — Guard `ConvertFCToConv` against zero-sized channel dimensions (LLVM abort fix)
- **[openvinotoolkit/npu_compiler#266](https://github.com/openvinotoolkit/npu_compiler/pull/266)** — Defense-in-depth: reject zero-dim FC ops in `UnrollFullyConnected`
- **[openvinotoolkit/openvino#34450](https://github.com/openvinotoolkit/openvino/issues/34450)** — Filed: LLVM abort in `as_convolution` pass for Qwen3-0.6B INT4 on Lunar Lake NPU
- **[openvinotoolkit/openvino#34617](https://github.com/openvinotoolkit/openvino/issues/34617)** — Filed: `compile_model` fails with dynamic shape error for Qwen3-0.6B INT4
- **[openvinotoolkit/openvino.genai#3429](https://github.com/openvinotoolkit/openvino.genai/issues/3429)** — Filed: `LLMPipeline` NPU draft model abort on Lunar Lake

Every bug I file comes from real workloads running real models on real hardware — not synthetic benchmarks.

---

### � How I Build

I practice **AI-assisted development** — I architect systems, make design decisions, and direct AI agents (GitHub Copilot, LLM-powered coding workflows) to implement, test, and operationalize the software. Every line of code in BlarAI was produced through human-directed AI collaboration: I own the architecture and every technical decision; AI agents own the keystrokes.

I'm transparent about this because I think it's the future of software engineering — and because the results speak for themselves: 780+ tests, 9 formally specified use cases, hardware-validated on real silicon.

---

### �🧭 What Drives Me

My passion for privacy fuels my research with local AI. I believe the average consumer device — the laptop already on your desk — can run powerful AI tools privately and securely, without surrendering your data to someone else's server.

I seek to contribute to open source projects that make this a reality for everyone.

---

### 📫 Connect

[![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/blair-do)
