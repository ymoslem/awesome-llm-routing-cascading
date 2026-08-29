# Awesome LLM Routing & Cascading

[![Paper](https://img.shields.io/badge/Paper-OpenReview-b31b1b)](https://openreview.net/forum?id=ypRg1TvQaM)

Curated list of papers on dynamic multi-LLM routing and cascading at inference time, methods, benchmarks, and evaluation metrics.

> Read the full paper: [**Dynamic Model Routing and Cascading for Efficient LLM Inference: A Survey**](https://openreview.net/forum?id=ypRg1TvQaM)  
> Yasmin Moslem, John D. Kelleher. *Transactions on Machine Learning Research*, 2026.



## Citation

```bibtex
@ARTICLE{Moslem-Kelleher-2026-RoutingSurvey,
  title   = "{Dynamic Model Routing and Cascading for Efficient LLM Inference: A Survey}",
  author  = "Moslem, Yasmin and Kelleher, John D",
  journal = "Transactions on Machine Learning Research (TMLR)",
  year    =  2026,
  url     = "https://openreview.net/forum?id=ypRg1TvQaM"
}
```

## Design-Space Framework

The paper is organized around the same six paradigms and design-space framework as the survey, so entries added here extend the paper's own taxonomy rather than a separate one. See the paper's Section 1.4 for the three axes used to characterize a method: **when** the routing decision is made (pre-generation, post-generation, multi-stage, online), **what** signals it uses (query, model metadata, response, feedback), and **how** the decision is computed (heuristic, supervised, bandit, RL policy). Every method in this list can be placed along the three axes below.

| Axis                          | Categories                                                   |
| ----------------------------- | ------------------------------------------------------------ |
| **When** the decision is made | Pre-generation · Post-generation · Multi-stage · Online/adaptive |
| **What** signals it uses      | Query · Model metadata · Response · Feedback                 |
| **How** it is computed        | Heuristic · Supervised · Bandit · RL policy                  |

A method can fill more than one cell per axis; that's a signal it composes multiple mechanisms within one pipeline. For the full discussion, please refer to the [paper](https://openreview.net/forum?id=ypRg1TvQaM)'s Section 10, "Towards Multidimensional Routing Systems". Here are some representative examples:

| Method     | When                                           | What signals           | How computed                           |
| ---------- | ---------------------------------------------- | ---------------------- | -------------------------------------- |
| RouteLLM   | Pre-generation                                 | Query                  | Supervised                             |
| UniRoute   | Pre-generation                                 | Query                  | Heuristic (unsupervised clustering)    |
| Eagle      | Pre-generation                                 | Query                  | Heuristic (ELO ranking, training-free) |
| PILOT      | Pre-generation, Online                         | Query + Feedback       | Bandit                                 |
| FrugalGPT  | Multi-stage                                    | Query + Response       | Supervised                             |
| Self-REF   | Post-generation                                | Response               | Supervised (fine-tuned)                |
| CRE-Router | Pre-generation + Post-generation (multi-stage) | Query + Response       | Supervised                             |
| Router-R1  | Multi-stage                                    | Query + Model metadata | RL policy                              |

## Contents

- [Design-Space Framework](#design-space-framework)
- [Difficulty-aware Routing](#difficulty-aware-routing)
- [Human Preference-aligned Routing](#human-preference-aligned-routing)
- [Clustering-based Routing](#clustering-based-routing)
- [Reinforcement Learning Routing](#reinforcement-learning-routing)
- [Uncertainty-based Routing](#uncertainty-based-routing)
- [Cascades](#cascades)
- [Multimodal Model Routing](#multimodal-model-routing)
- [LoRA Adapter Routing](#lora-adapter-routing)
- [Benchmarks](#benchmarks)

---

## Difficulty-aware Routing

Pre-generation methods that route based on estimated query complexity.

- **[BEST-Route](https://openreview.net/forum?id=tFBIbCVXkG)** — DeBERTa-v3-small multi-head router selects both the model and a best-of-*n* sampling budget under a user-set cost/accuracy threshold. *(ICML 2025)*
- **[HAPS](http://dx.doi.org/10.48550/arXiv.2601.05903)** — jointly trained hierarchical routers select an LLM and generate input-conditioned LoRA adapter weights for it. *(arXiv 2026)*
- **[Combining the Best of Both Worlds](https://aclanthology.org/2025.findings-acl.266/)** — source-side routing between an NMT system and an LLM based on source-sentence complexity. *(ACL Findings 2025)*
- **[RouteLMT](https://openreview.net/forum?id=0SMriQrEjL)** — in-model router probing a small translator's prompt-token representations via LoRA, using the marginal gain over the small model as the routing signal. *(ACL Industry 2026)*
- **[vLLM Semantic Router ("When to Reason")](https://openreview.net/forum?id=MGeOrfovNn)** — ModernBERT classifier decides whether a query needs chain-of-thought reasoning before routing. *(NeurIPS ML for Systems Workshop 2025)*
- **[LLM Routing with Benchmark Datasets](https://openreview.net/forum?id=Zb0ajZ7vAt)** — recasts router training as per-model binary correctness classification via k-NN. *(COLM 2024)*
- **[PORT](https://proceedings.neurips.cc/paper_files/paper/2025/hash/c878e34a1b2095ae8177961ba24d642d-Abstract-Conference.html)** — scales k-NN performance/cost estimation to high-volume online serving without training a parametric router. *(NeurIPS 2025)*
- **[EmbedLLM](https://openreview.net/forum?id=Fs9EabmQrJ)** — matrix factorization learns compact LLM embeddings to predict query-model correctness. *(ICLR 2025)*
- **[RouterDC](https://proceedings.neurips.cc/paper_files/paper/2024/hash/7a641b8ec86162fc875fb9f6456a542f-Abstract-Conference.html)** — dual contrastive learning trains a shared query encoder alongside per-LLM embeddings. *(NeurIPS 2024)*
- **[ICL-Router](https://ojs.aaai.org/index.php/AAAI/article/view/40628/)** — represents model capability as in-context vectors so new models can be added without retraining the router. *(AAAI 2026)*
- **[GraphRouter](https://openreview.net/forum?id=eU39PDsZtT)** — heterogeneous graph over tasks, queries, and LLMs; a GNN predicts routing effectiveness and cost inductively. *(ICLR 2025)*
- **[IRT-Router](https://aclanthology.org/2025.acl-long.761/)** — Item Response Theory formulation modeling LLM ability and query difficulty/discrimination jointly. *(ACL 2025)*

## Human Preference-aligned Routing

Pre-generation methods trained on human or synthetic preference data.

- **[RouteLLM](https://openreview.net/forum?id=8sSqNntaMr)** — win-prediction model trained on Chatbot Arena preferences plus LLM-judge labels to route between a strong and weak model. *(ICLR 2025)*
- **[Meta-Router](https://openreview.net/forum?id=r0BFucF2dH)** — treats the choice between scarce gold-standard labels and biased preference comparisons as a treatment-assignment problem. *(ICLR 2026)*
- **[Causal LLM Routing](https://openreview.net/forum?id=iZC5xoQQkX)** — learns from observational (single-model-per-query) data via a doubly-robust causal estimator and end-to-end regret minimization. *(NeurIPS 2025)*
- **[Arch-Router](http://dx.doi.org/10.48550/arXiv.2506.16655)** — routes via user-defined domain-action policies given as input context to a 1.5B router LLM, updatable without retraining. *(arXiv 2025)*
- **[Hybrid LLM](https://openreview.net/forum?id=02f3mUtqnM)** — LLM-based classifier trained on synthetic preference data for cost-efficient query routing. *(ICLR 2024)*
- **[Prompt-to-Leaderboard (P2L)](https://openreview.net/forum?id=7VPRrzFEN8)** — generates prompt-specific Bradley-Terry coefficients for personalized, task-specific model ranking. *(ICML 2025)*
- **[GMTRouter](http://dx.doi.org/10.48550/arXiv.2511.08590)** — heterogeneous graph over multi-turn user-LLM interaction histories for inductive, per-user personalization. *(arXiv 2025)*
- **[Eagle](https://mlforsystems.org/assets/papers/neurips2024/paper38.pdf)** — training-free router combining global and local ELO rankings from historical pairwise comparisons. *(NeurIPS ML for Systems Workshop 2024)*
- **[Zooter](https://aclanthology.org/2024.naacl-long.109)** — distills a routing classifier from a reward model's response scores rather than optimizing preferences directly. *(NAACL 2024)*

## Clustering-based Routing

Unsupervised query clustering with per-cluster model assignment.

- **[UniRoute](https://openreview.net/forum?id=ka82fvJ5f1)** — K-means clustering with per-cluster, cost-adjusted error scores; generalizes to unseen LLMs without retraining. *(ICLR 2026)*
- **[The Avengers](http://dx.doi.org/10.1609/aaai.v40i41.40790)** — embed/cluster/rank/route recipe combining smaller open-source models into a collective ensemble. *(AAAI 2026)*
- **[Avengers-Pro / Beyond GPT-5](https://dl.acm.org/doi/10.1145/3772429.3772445)** — extends the Avengers recipe with a single-hyperparameter performance-efficiency trade-off. *(DAI 2025)*

## Reinforcement Learning Routing

### Policy Optimization

- **[Router-R1](https://openreview.net/forum?id=DWf4vroKWJ)** — an LLM router alternates "think" and "route" actions across up to 4 steps, trained with PPO. *(NeurIPS 2025)*
- **[Route-and-Reason (R2-Reasoner)](http://dx.doi.org/10.48550/arXiv.2506.05901)** — decomposes tasks into subtasks and allocates each via a GRPO-trained Subtask Allocator. *(arXiv 2025)*
- **[SCOPE](http://dx.doi.org/10.48550/arXiv.2601.22323)** — GRPO trains a performance/cost estimator from behavioral model fingerprints, not a routing policy directly. *(arXiv 2026)*
- **[xRouter](http://dx.doi.org/10.48550/arXiv.2510.08439)** — tool-calling formulation where an RL-trained router can answer directly or invoke external models under a cost-aware reward. *(arXiv 2025)*

### Bandit-Based

- **[MetaLLM](http://dx.doi.org/10.48550/arXiv.2407.10834)** — multi-armed bandit trades off accuracy and cost using a reward computed directly from ground-truth correctness. *(arXiv 2025)*
- **[MixLLM](https://aclanthology.org/2025.naacl-long.545/)** — contextual bandit with policy gradients over domain-aware query embeddings, updated from online user feedback. *(NAACL 2025)*
- **[PILOT](https://aclanthology.org/2025.findings-emnlp.1301/)** — LinUCB contextual bandit incorporating offline preference priors (Chatbot Arena) and an online-knapsack cost model. *(EMNLP Findings 2025)*
- **[GreenServ](http://dx.doi.org/10.48550/arXiv.2601.17551)** — LinUCB bandit optimizing directly for measured GPU energy consumption rather than price/token proxies. *(arXiv 2026)*
- **[LLM Routing with Dueling Feedback](http://dx.doi.org/10.48550/arXiv.2510.00841)** — contextual dueling bandit (FGTS.CDB) learning from pairwise preference comparisons via category-calibrated embeddings. *(arXiv 2025)*
- **[TI-UCB](https://dl.acm.org/doi/10.1145/3589334.3645420)** — time-increasing UCB bandit with change detection for non-stationary rewards during iterative model fine-tuning. *(WWW 2024)*
- **[Contextual Queueing Bandits](http://dx.doi.org/10.48550/arXiv.2602.02061)** — jointly decides serving order and routing, learning from implicit feedback in user retrials. *(arXiv 2026)*

## Uncertainty-based Routing

Post-generation methods using response-level confidence signals.

- **[Confident or Seek Stronger](http://dx.doi.org/10.48550/arXiv.2502.04428)** — benchmarks eight uncertainty-quantification methods for SLM-to-LLM edge routing. *(arXiv 2025)*
- **[CP-Router](http://dx.doi.org/10.48550/arXiv.2505.19970)** — Conformal Prediction over answer-option logits routes to a Large Reasoning Model only when the prediction set is large. *(arXiv 2025)*
- **[Leveraging Uncertainty Estimation for Efficient LLM Routing](https://openreview.net/forum?id=DJpEIwKJt7)** — pre-generation classifier trained on semantic-entropy-derived preference labels between a strong and weak model. *(ICML Workshop 2025)*
- **[Learning to Route LLMs with Confidence Tokens (Self-REF)](https://openreview.net/forum?id=U08mUogGDM)** — lightweight fine-tuning teaches an LLM to emit confidence tokens used for routing/rejection. *(ICML 2025)*

## Cascades

Sequential multi-model pipelines that escalate based on response quality.

- **[FrugalGPT](https://openreview.net/forum?id=cSimKw5p6R)** — a fixed, offline-learned model list plus a DistilBERT quality scorer with a jointly-learned escalation threshold. *(TMLR 2024)*
- **[Cascade Routing](https://openreview.net/forum?id=AAl89VNNy1)** — unifies routing and cascading into one framework that iteratively selects the best model at each step. *(ICML 2025)*
- **[CRE-Router](http://dx.doi.org/10.48550/arXiv.2606.27457)** — clustering-based pre-generation routing composed with a ModernBERT quality-estimation cascade; blends TPOT and E2EL cost metrics. *(arXiv 2026)*
- **[SATER](https://aclanthology.org/2025.emnlp-main.531/)** — fine-tunes the pool models directly with shortest-response preference optimization and a built-in rejection mechanism. *(EMNLP 2025)*
- **[AutoMix](https://openreview.net/forum?id=e6WrwIvgzX)** — few-shot self-verification plus a POMDP router escalates to a larger model without any fine-tuning. *(NeurIPS 2024)*
- **[Model Cascading for Code](https://arxiv.org/pdf/2405.15842)** — escalation signal comes from executing each model's own generated test cases rather than a learned scorer. *(IJCNN 2025)*
- **[LLM-Blender](https://aclanthology.org/2023.acl-long.792/)** — ensembles multiple LLM outputs via pairwise ranking (PairRanker) and generative fusion (GenFuser). *(ACL 2023)*
- **[R2R](https://proceedings.neurips.cc/paper_files/paper/2025/hash/b39cef2ef90591cffdc9c674cd55bebe-Abstract-Conference.html)** — token-level routing between a small and large model, escalating only where their reasoning paths diverge. *(NeurIPS 2025)*
- **[Translate Smart, Not Hard](https://aclanthology.org/2025.emnlp-main.1358/)** — quality-aware deferral rules route between small and large translation models. *(EMNLP 2025)*

## Multimodal Model Routing

- **[Model-Spider](https://proceedings.neurips.cc/paper_files/paper/2023/hash/2c71b14637802ed08eaa3cf50342b2b9-Abstract-Conference.html)** — ranks pre-trained models (vision and language) for a downstream task without dynamic per-query dispatch. *(NeurIPS 2023)*
- **[ReLope](http://dx.doi.org/10.48550/arXiv.2603.24787)** — KL-regularized LoRA probes recover correctness signals that degrade in multimodal LLM hidden states. *(arXiv 2026)*

## LoRA Adapter Routing

Routing among a pool of adapters sharing one base model — a related but distinct problem from routing between independently trained LLMs.

- **[LoraRetriever](https://aclanthology.org/2024.findings-acl.263/)** — retrieves and composes adapters via a contrastively-trained encoder over each adapter's own training samples. *(ACL Findings 2024)*
- **[LoRAuter](http://dx.doi.org/10.48550/arXiv.2601.21795)** — task-level retrieval from lightweight validation sets removes the need for a dedicated retrieval encoder. *(arXiv 2026)*
- **[POLAR](http://dx.doi.org/10.48550/arXiv.2604.16583)** — two-timescale contextual bandit jointly decides adapter caching and routing under edge-serving memory constraints. *(arXiv 2026)*

## Benchmarks

- **[RouterBench](https://openreview.net/forum?id=IVXmV8Uxwh)** — 405k+ precomputed outputs across 11 LLMs and 7 tasks with cost metadata. *(ICML Workshop 2024)*
- **[RouterEval](https://aclanthology.org/2025.findings-emnlp.208/)** — 200M+ performance records across 8,500+ LLMs, framing routing as m-way classification. *(EMNLP Findings 2025)*
- **[MixInstruct](https://aclanthology.org/2023.acl-long.792/)** — 110k instruction-following examples with oracle pairwise preferences. *(ACL 2023)*
- **[LLMRouterBench](https://aclanthology.org/2026.findings-acl.1881/)** — 400k+ instances across 21 datasets and 33 models with a standardized collector/evaluator/adaptor framework. *(ACL Findings 2026)*
- **[RouterArena](https://openreview.net/forum?id=9HsaIi4ngF)** — open leaderboard-style platform across a broad range of knowledge domains and difficulty levels. *(ICLR 2026)*
- **[DSC Benchmark](https://aclanthology.org/2026.eacl-long.351/)** — extends evaluation to privacy/safety, showing preference-based routers can misroute jailbreak attempts to less-safe models. *(EACL 2026)*
- **[Who Routes the Router](https://openreview.net/forum?id=EEPostHMtF)** — critiques existing benchmarks' task diversity and model-pool balance; proposes 68 categories × 85 models. *(NeurIPS Workshop 2025)*
- **[MMR-Bench](http://dx.doi.org/10.48550/arXiv.2601.17814)** — modality-aware benchmark covering OCR, VQA, and multimodal math reasoning. *(ECCV 2026)*
- **[VL-RouterBench](http://dx.doi.org/10.48550/arXiv.2512.23562)** — 14 datasets, 30,540 samples, 17 candidate VLMs for vision-language routing. *(CVPR 2026)*

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — matching the companion survey's license.
