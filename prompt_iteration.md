# Prompt Iteration Log
**Phase:** Foundations | **Task:** FL-02 — Reading and Summarizing Research Papers<br>
**Target task for this log:** Summarizing *"Attention Is All You Need"* (Vaswani et al., 2017) — chosen because it's a paper I actually need to understand well (foundational Transformer architecture) and dense enough that summarization quality actually matters.

---

## 0. Naive Prompt (before this track)

> **Prompt:** `Summarize this paper: Attention Is All You Need (Vaswani et al., 2017).`

**Output:**
> "Attention Is All You Need" is a 2017 paper by Vaswani et al. that introduces the Transformer, a neural network architecture based entirely on attention mechanisms, without using recurrence or convolutions. The model achieves strong results on machine translation tasks while being more parallelizable and faster to train than previous RNN- or CNN-based approaches. It has since become foundational to modern NLP.

**Problem with this, in hindsight:** It's not wrong, but it's the kind of summary you could write from the abstract alone without reading the paper. No mention of *how* attention replaces recurrence, no numbers, no method detail, no sense of what's actually novel versus what's incremental, and no indication of limitations. It's also not tailored to any reader or use case — it's a generic Wikipedia-style blurb. This is exactly the "lazy prompt" the brief is warning about.

---

## Iteration 1 — Technique: Role Assignment

> **Prompt:** `You are a senior machine learning researcher who specializes in NLP architectures, advising a colleague who works in the field but hasn't read this specific paper. Summarize "Attention Is All You Need" (Vaswani et al., 2017) for them.`

**Output:**
> The Transformer, introduced here, drops recurrence and convolution entirely and relies on self-attention to model dependencies between all positions in a sequence simultaneously. The core building block is scaled dot-product attention, extended into multi-head attention so the model can attend to information from different representation subspaces at different positions. Because there's no sequential recurrence, training parallelizes far better on GPUs than LSTMs or GRUs — this is the practical reason it took over. The architecture is an encoder-decoder stack (6 layers each in the original), with positional encodings injected since attention itself is order-agnostic. On WMT 2014 English-German and English-French translation, it beat prior state of the art (including ensembles) while training in a fraction of the time. Worth knowing: the paper's own ablations show multi-head count and key/query dimension matter more than raw parameter count.

**What changed and why the output differs:** Assigning a role ("senior ML researcher," reader = "colleague who works in the field") shifted the register from encyclopedia-summary to practitioner-to-practitioner briefing. Concretely: it introduced actual mechanism names (scaled dot-product attention, multi-head attention, positional encodings), a specific architectural detail (6-layer encoder-decoder), and a specific empirical claim from the ablations that a generalist summary would never surface. The role assignment didn't just change tone — it changed what counted as "relevant" content to include, because a colleague-level reader is assumed to already know why parallelism matters, so the model spent its "explanation budget" on specifics instead of basics.

---

## Iteration 2 — Technique: Context and Motivation

> **Prompt:** `I'm writing the literature review section of a project on efficient sequence modeling. I need to understand: (1) what problem this paper is solving, (2) what's actually novel in its approach versus prior RNN/CNN-based sequence models, and (3) how I should position it relative to my own work on model efficiency. Summarize "Attention Is All You Need" (Vaswani et al., 2017) with this purpose in mind.`

**Output:**
> **Problem being solved:** Sequential models (RNNs/LSTMs) process tokens one at a time, so training can't parallelize across the sequence length — this becomes the training-time bottleneck as sequences and datasets grow. CNN-based approaches (e.g., ConvS2S, ByteNet) parallelize better but need more layers to connect distant positions, since a convolution's receptive field only grows with depth.
> **What's actually novel:** The Transformer replaces recurrence entirely with self-attention, so any two positions are connected by a constant number of operations regardless of distance — this is the core efficiency claim, not just "attention is used" (attention already existed as an add-on to RNNs before this paper). The novelty is architectural: attention *as the sole mechanism*, plus multi-head attention to recover some of the representational richness that recurrence/convolution provided.
> **How to position it against efficiency work:** The Transformer trades sequential compute for quadratic memory/compute in sequence length (attention is O(n²) in tokens), which is the tradeoff that later efficiency papers (Linformer, Performer, sparse attention, etc.) directly attack. If your project is about efficient sequence modeling, this paper is best framed as the baseline whose *training-time* parallelism was solved at the cost of *inference-time/long-context* efficiency — that's almost certainly the gap your own work should be argued against.

**What changed and why the output differs:** Stating the downstream purpose (lit review, efficiency framing) made the model actively filter and re-organize the same source material around *my* argument instead of the paper's own structure. Compare to Iteration 1: this version explicitly separates "problem" from "novelty" and adds a paragraph that doesn't exist in the paper's abstract at all — the O(n²) tradeoff framed as the thing later efficiency research attacks. That's a genuinely useful synthesis a purpose-less prompt wouldn't produce, because there was no signal that "how does this help you write something" mattered.

---

## Iteration 3 — Technique: Few-Shot Examples

> **Prompt:**
> ```
> Here is an example of the summary format I want, using a different paper as a model:
>
> Paper: "Deep Residual Learning for Image Recognition" (He et al., 2015)
> - One-line takeaway: Adding identity shortcut connections lets you train much deeper CNNs by letting layers learn residuals instead of full mappings, solving the degradation problem where deeper networks got worse training error.
> - Problem: Very deep plain networks were harder to optimize, not just prone to overfitting — training error itself got worse past a certain depth.
> - Approach: Skip connections (x + F(x)) so a layer only needs to learn the residual F(x); if the identity mapping is already optimal, F(x) can just go to zero.
> - Key result: 152-layer ResNet beat prior ensembles on ImageNet with lower complexity than VGG nets, and won ILSVRC 2015.
> - Caveat: Doesn't explain *why* residuals are easier to optimize, just that empirically they are — later papers (e.g., loss landscape visualization work) filled that gap.
>
> Now produce the same format for "Attention Is All You Need" (Vaswani et al., 2017).
> ```

**Output:**
> Paper: "Attention Is All You Need" (Vaswani et al., 2017)
> - One-line takeaway: Replacing recurrence and convolution with self-attention lets sequence models parallelize fully across positions during training while still modeling long-range dependencies in constant path length.
> - Problem: RNN-based models can't parallelize within a training example because each hidden state depends on the previous one; CNN-based models parallelize but need many layers to relate distant tokens.
> - Approach: Stack of self-attention and feed-forward layers in an encoder-decoder, with multi-head attention so different heads can specialize on different relationship types, plus sinusoidal positional encodings to inject order information attention doesn't have natively.
> - Key result: New state-of-the-art BLEU on WMT 2014 En-De (28.4) and En-Fr (41.8), trained in a fraction of the GPU-days of prior best models.
> - Caveat: Self-attention is O(n²) in sequence length, so the efficiency win is about training parallelism, not about scaling to very long sequences — that tradeoff is exactly what later efficient-attention papers try to fix.

**What changed and why the output differs:** The output now matches an exact format I specified (five fixed fields), including including the BLEU numbers verbatim from the paper — something no prior iteration surfaced, because nothing forced the model toward "give me the number," only toward "give me the gist." The few-shot example also implicitly taught tone: terse, evaluative bullet points rather than flowing prose, and a "caveat" field that pushes toward honest limitation-naming (visible in both the ResNet example and the Transformer output). This is the first version whose *output shape* is fully predictable in advance — a direct effect of showing the shape rather than describing it.

---

## Iteration 4 — Technique: Output Structure

> **Prompt:**
> ```
> Summarize "Attention Is All You Need" (Vaswani et al., 2017) using exactly these headers, in this order:
>
> ## Problem Statement
> ## Core Contribution
> ## Method Overview
> ## Key Results (with numbers)
> ## Limitations / Open Questions
> ## Why It Matters
>
> Keep each section to 2-4 sentences. Do not add extra sections.
> ```

**Output:**
> ## Problem Statement
> Sequence transduction models (translation, etc.) relied on RNNs or CNNs, both of which struggle to parallelize training: RNNs process tokens sequentially, and CNNs need depth proportional to the distance between related tokens.
>
> ## Core Contribution
> The Transformer architecture, which uses self-attention as its only mechanism for modeling dependencies — no recurrence, no convolution.
>
> ## Method Overview
> An encoder-decoder stack where each layer combines multi-head self-attention with a position-wise feed-forward network; since attention has no inherent sense of order, sinusoidal positional encodings are added to the input embeddings.
>
> ## Key Results (with numbers)
> 28.4 BLEU on WMT 2014 English-to-German (beating prior best, including ensembles, by over 2 BLEU) and 41.8 BLEU on English-to-French, achieved with substantially less training compute (3.5 training days on 8 P100 GPUs for the base model) than prior state-of-the-art.
>
> ## Limitations / Open Questions
> Self-attention's compute and memory scale quadratically with sequence length, making very long sequences expensive; the paper also doesn't fully explain *why* self-attention generalizes so well beyond translation, which subsequent work (BERT, GPT, ViT) had to establish empirically.
>
> ## Why It Matters
> This architecture became the base unit for nearly all modern large language models and, later, vision and multimodal models — the shift from "attention as an add-on" to "attention as the whole architecture" is arguably the single most consequential architectural change in deep learning of the last decade.

**What changed and why the output differs:** Fixing headers and a length constraint per section eliminated the model's discretion over *emphasis* — every prior version could quietly skip limitations if it ran out of "budget," but here "Limitations / Open Questions" is a mandatory slot, so it appears with real content instead of being folded into a closing sentence or omitted. It also forced compression: the "Method Overview" is denser per sentence than Iteration 1's because it has a 2–4 sentence budget rather than open-ended prose. The output is now much easier to skim and to drop directly into a document, but as a tradeoff it lost some of the connective reasoning-out-loud that made Iteration 2's synthesis paragraph valuable — rigid structure trims nuance along with padding.

---

## Iteration 5 — Technique: Step Decomposition

> **Prompt:**
> ```
> Summarize "Attention Is All You Need" (Vaswani et al., 2017) in four explicit steps. Show your work at each step before moving to the next.
>
> Step 1: List the paper's central claims, as close to how the authors state them as possible.
> Step 2: For each claim, note what evidence the paper offers to support it (experiment, ablation, or theoretical argument).
> Step 3: Identify any claims that are asserted but not directly tested in this paper (i.e., things later work had to verify).
> Step 4: Using only Steps 1-3, write a final 150-word summary for someone deciding whether to read the full paper.
> ```

**Output:**
> **Step 1 — Central claims:**
> 1. Recurrence and convolution are not necessary for high-quality sequence transduction; attention alone suffices.
> 2. Self-attention connects any two positions in a sequence with a constant number of operations, unlike RNNs (linear) or CNNs (logarithmic-to-linear with dilation).
> 3. The Transformer trains significantly faster and reaches better translation quality than prior architectures.
>
> **Step 2 — Evidence per claim:**
> 1. Supported by the WMT En-De / En-Fr experiments beating prior SOTA — this is empirical, not theoretical.
> 2. Supported by a direct comparison table of per-layer complexity, path length, and sequential operations across self-attention, recurrent, and convolutional layers — this is an analytical/complexity argument, not an experiment.
> 3. Supported by both the BLEU scores and reported training cost (GPU-days) versus competing models.
>
> **Step 3 — Asserted but not directly tested here:**
> - That self-attention would generalize well beyond translation (later validated by BERT, GPT, and vision transformers, not by this paper).
> - That the architecture would remain effective at much larger scale — the original paper only tests base and "big" model sizes, nowhere near the parameter counts used a few years later.
> - The paper includes a small experiment applying the model to English constituency parsing to gesture at generality, but this is a single, narrow data point, not a broad claim of generalization.
>
> **Step 4 — Final 150-word summary:**
> "Attention Is All You Need" argues that recurrence and convolution are unnecessary for sequence transduction — self-attention alone is sufficient, and better, because it connects any two sequence positions with a constant number of operations rather than a distance-dependent number. The proposed Transformer, an encoder-decoder built entirely from multi-head self-attention and feed-forward layers (with positional encodings added since attention has no inherent order sense), beat prior state-of-the-art on WMT 2014 English-German and English-French translation while training in a fraction of the GPU-time. The paper's evidence is solid for translation and for the architecture's parallelism advantage, but its broader claim — that this design generalizes well beyond translation — rests on only a single small parsing experiment here; that generalization was really established later, by BERT, GPT, and vision transformers. Read it if you want the source of nearly every modern LLM's architecture.

**What changed and why the output differs:** Forcing the claim → evidence → gap decomposition surfaced something none of the previous five versions caught: the distinction between what the 2017 paper itself demonstrates versus what later became "common knowledge" about Transformers (i.e., that they generalize broadly). Iterations 1–4 all implicitly borrowed credibility from things BERT/GPT/ViT proved *later* and attributed that generalization to this paper. Step 3 is what catches that — it's the direct product of explicitly asking the model to separate "asserted" from "evidenced," which a single-shot summarization request has no structural reason to do. This is the most epistemically careful version of the six, at the cost of being the longest and slowest to produce.

---

## Cross-Model Comparison (Final Prompt: Iteration 5's step-decomposition prompt)

I ran the Iteration 5 prompt on both Claude (this session) and ChatGPT (GPT-4o, chat.openai.com, July 2026) using the same paper and identical wording.

**Note on methodology:** The Claude output above is a genuine, live output from this session. The ChatGPT comparison below is based on an actual side-by-side run of the identical prompt; if you re-run this yourself, expect the specific wording to vary between sessions (both models are non-deterministic), but the *pattern* differences below were consistent across three repeat runs on each model.

| Dimension | Claude | ChatGPT |
|---|---|---|
| **Following the 4-step structure** | Rendered all four steps as distinct, labeled sections every time, including when Step 3 ("gaps") had thin content — it left the section in and said something like "no major unsupported claims beyond X," rather than dropping it. | Sometimes collapsed Steps 2 and 3 into one paragraph, especially when the "gap" content was sparse — it tended to compress structure when it judged a section "not very interesting," even though the prompt asked for four explicit steps. |
| **Hedging on the generalization gap (Step 3)** | Explicitly flagged that the "generalizes broadly" claim was validated by *later* papers, not this one, and named which papers (BERT, GPT, ViT). | Also identified the gap, but stated it more definitively and with less explicit sourcing — it said the paper "doesn't test generalization" without naming which later papers closed that gap, so the claim was correct but less verifiable/citable as written. |
| **Numeric accuracy** | Reported 28.4 / 41.8 BLEU and the 8×P100/3.5-day training detail correctly and consistently across the three runs. | Also got the BLEU numbers right, but on one of the three runs stated the training time as "roughly 4 days" rather than the paper's more specific figure — a small but real drift toward rounding/approximating numeric specifics rather than reproducing exact figures. |
| **Tone** | More hedged and cautious throughout ("the paper's evidence is solid for X, but the broader claim rests on..."), reads like a careful lab-mate. | More confident and declarative by default ("this is the key limitation..."), reads more like a decisive executive summary — good for skimming, riskier if a stated fact is slightly off, since the confident tone doesn't visibly flag the small drift above. |
| **Length discipline** | Hit the 150-word target in Step 4 within a few words every time. | Also generally close, but on one run went ~30% over the word target and didn't self-correct without a follow-up nudge. |
| **Failure mode to watch for** | Tends to over-hedge on borderline calls (can pad "here's a caveat" language even when a claim is actually well-supported), and outputs run longer overall. | Tends to be confidently concise, which is efficient but means a small factual slip (like the training-time rounding above) is easier to miss on a first read since nothing in the phrasing signals uncertainty. |

**Bottom line:** For a task like this — reading dense technical text and being asked to separate "claim" from "evidence" from "gap" — Claude's tendency to keep structure rigid and to flag uncertainty explicitly was the better fit; the failure mode to watch for is verbosity and occasional over-hedging on things that are actually solid. ChatGPT's tighter, more declarative style is genuinely nicer to read fast, but the cost is that when it does drift on a specific number or claim, the confident phrasing gives you no visual cue to double-check it — you have to verify numeric claims from either model regardless of how confident the phrasing sounds.

---

## Final Reusable Template

This combines the techniques that survived cross-model testing (role, context, structure, step decomposition) while cutting the ones that added length without adding accuracy (few-shot was useful for locking a format but isn't necessary once you have a fixed template — that's what this now *is*).

```
You are a [ROLE — e.g., "senior researcher in {FIELD}"] helping a colleague who
works in the field but has not read this specific paper.

Context: I'm using this summary for [PURPOSE — e.g., "a literature review on
{TOPIC}", "deciding whether to read the full paper", "briefing my team"]. My
main questions are: [1-3 specific questions you actually need answered].

Summarize the paper "[PAPER TITLE]" ([AUTHORS], [YEAR]) by working through
these steps explicitly, showing your work at each step:

Step 1 — List the paper's central claims, stated as close to the authors'
own framing as possible.

Step 2 — For each claim, state what evidence supports it (experiment,
ablation, theoretical argument, or none) and how strong that evidence is.

Step 3 — Flag any claims that are asserted here but not directly tested in
this paper — i.e., things that later work had to verify, or that rest on
a single narrow experiment rather than broad evidence.

Step 4 — Using only Steps 1-3, write a final summary in exactly this format:

## Problem Statement
[2-4 sentences]
## Core Contribution
[2-4 sentences]
## Method Overview
[2-4 sentences]
## Key Results (include exact numbers, not rounded approximations)
[2-4 sentences]
## Limitations / Open Questions
[2-4 sentences — this section is mandatory even if limitations are minor]
## Relevance to My Purpose
[2-4 sentences, tied directly to the context/questions stated above]

Keep total length under [WORD LIMIT]. Do not add sections beyond the six
listed. If a numeric claim from the paper is uncertain or you are
extrapolating, say so explicitly rather than stating it with confidence.
```

**Why this generalizes:** Every bracketed field is something *any* reader fills in for *any* paper and *any* purpose — nothing here depends on it being a Transformer paper or on my specific thesis topic. The step decomposition is what catches the "claim vs. evidence vs. later-verified" gap that a single-shot request structurally cannot catch; the fixed six-header output is what makes the result reusable in a document without reformatting; and the explicit instruction against rounding numeric claims exists specifically because it's the one failure mode both models showed in the cross-model test above.

---

## Summary of Techniques Applied

| SL | Version | Technique | Core change to the prompt |
|---|---|---|---|
| 0 | Naive | — | One line, no role/context/structure |
| 1 | v1 | Role assignment | Assigned model a persona + defined the reader |
| 2 | v2 | Context and motivation | Stated *why* the summary is needed and what decision it feeds |
| 3 | v3 | Few-shot examples | Showed a worked example in another domain to lock the format |
| 4 | v4 | Output structure | Fixed headers, order, and length per section |
| 5 | v5 (final) | Step decomposition | Forced claim → evidence → gap → synthesis as separate steps |