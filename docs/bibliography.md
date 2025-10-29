# Bibliography: AI ADHD and Executive Dysfunction Research

**Last Updated:** October 24, 2025
**Research Context:** Literature review for Anastrophex project

This bibliography compiles all academic papers, articles, and sources referenced in our research on AI systems exhibiting ADHD-like symptoms (impulsivity, executive dysfunction, loop behaviors, and metacognitive deficits).

---

## Table of Contents

1. [Popular Media and Industry Articles](#popular-media-and-industry-articles)
2. [Executive Dysfunction in LLMs](#executive-dysfunction-in-llms)
3. [Metacognition and Loop Detection](#metacognition-and-loop-detection)
4. [System 1 / System 2 Dual-Process Frameworks](#system-1--system-2-dual-process-frameworks)
5. [Working Memory and Cognitive Load](#working-memory-and-cognitive-load)
6. [Inference Scaling and Reasoning](#inference-scaling-and-reasoning)
7. [Neuropsychological Testing of AI](#neuropsychological-testing-of-ai)
8. [Practical Interventions and Architectures](#practical-interventions-and-architectures)
9. [Foundational Cognitive Science](#foundational-cognitive-science)

---

## Popular Media and Industry Articles

### The Term "AI ADHD" in Existing Literature

**Boyd, E. (2025, May).** The LLM Overload: How 'AI ADHD' is Draining Developer Productivity. *Medium*.
https://medium.com/@seasonedsolutionsadvisor/the-llm-overload-how-ai-adhd-is-draining-developer-productivity-7e86d26e8f05

**Context:** Uses "AI ADHD" to describe *developers* experiencing ADHD-like symptoms from managing multiple LLMs, not AI systems exhibiting ADHD symptoms. Our concept appears novel.

---

## Executive Dysfunction in LLMs

### Neuropsychological Evidence

**Vazquez, H. C. (2023).** Artificial Neuropsychology: Are Large Language Models Developing Executive Functions? *arXiv preprint arXiv:2305.04134v2*.
http://arxiv.org/pdf/2305.04134v2

**Key finding:** "Poor planning abilities identified as a particular weakness" in GPT-3.5 when tested on Tower of Hanoi task. LLMs show "limited" executive functions compared to well-trained humans.

---

### Impulsivity: Reducing Effort Under Difficulty

**MIT News. (2024, July 11).** Reasoning skills of large language models are often overestimated.
https://news.mit.edu/2024/reasoning-skills-large-language-models-often-overestimated-0711

**Key finding:** Apple Research finds advanced reasoning LLMs exhibit "striking collapse" as problems get harder - they **reduce reasoning effort** rather than thinking more deeply. This is functionally equivalent to impulsivity.

---

### Planning and Hierarchical Task Decomposition

**Silver, T., Hariprasad, V., Shuttleworth, R. S., Kumar, N., Lozano-Pérez, T., & Kaelbling, L. P. (2024).** Modular Agentic Planner (MAP): A Brain-Inspired Architecture for Planning in Large Language Models. *arXiv preprint arXiv:2410.13272v1*.
http://arxiv.org/pdf/2410.13272v1

**Key finding:** Brain-inspired modular planning improves GPT-4 from 5% success rate to 46% on Tower of Hanoi by decomposing planning into specialized modules (analogous to prefrontal cortex specialization).

---

## Metacognition and Loop Detection

### Agentic Metacognition

**[Author TBD]. (2024).** Agentic Metacognition: Designing a 'Self-Aware' Low-Code. *arXiv preprint arXiv:2509.19783*.
http://arxiv.org/pdf/2509.19783

**Key finding:** "Repetitive action sequences serve as a powerful signal of an agent being stuck in an infinite loop." Describes intervention strategies including human handoff when metacognitive monitoring identifies persistent failures.

**Quote:** "If the primary agent attempts to invoke the same API with the same parameters more than a certain number of times (e.g., three times), the metacognitive agent will flag this as a failure."

**Directly validates Anastrophex's loop detection approach.**

---

### Dual-Process Thinking with Metacognition

**[Authors TBD]. (2025).** Fast, slow, and metacognitive thinking in AI. *npj Artificial Intelligence*.
https://www.nature.com/articles/s44387-025-00027-5

**Key insight:** Metacognition is a **third dimension** beyond System 1/System 2:
- System 1: Fast, intuitive, pattern-based
- System 2: Slow, deliberate, reasoning-based
- **Metacognition:** Monitoring, error detection, strategy adjustment

**Implication:** Anastrophex provides the metacognitive layer that LLMs lack.

---

### Repetition Mechanisms

**[Authors TBD]. (2024).** Analysis of repetition in large language models. [Cited in Perplexity Deep Research report]

**Key finding:** Text degenerates into repetitive loops through **distinct underlying mechanisms**:
1. **Natural repetition:** Peaked probability distributions, diffused attention activation
2. **Induced repetition (in-context learning):** Sparse activation, initially low confidence

**Implication:** Different loop types require different intervention strategies (parallels ADHD subtypes).

---

## System 1 / System 2 Dual-Process Frameworks

### Cognitive Decision Routing

**Du, Y., et al. (2025).** Cognitive Decision Routing in Large Language Models: When to Think Fast, When to Think Slow. *arXiv preprint arXiv:2508.16636v1*.
http://arxiv.org/pdf/2508.16636v1

**Key finding:** Dynamically determines when to use intuitive vs. deliberate reasoning. Reduces computational costs by 34% while improving accuracy. 23% consistency improvement on professional judgment tasks.

**Relevance:** Validates adaptive intervention approach (when to slow down vs. when to let it go fast).

---

### Synergy-of-Thoughts (Hybrid Approach)

**[Authors TBD]. (2024).** Synergy-of-Thoughts: Eliciting Efficient Reasoning in Hybrid Language Models. *arXiv preprint arXiv:2402.02563v4*.
http://arxiv.org/pdf/2402.02563v4

**Key finding:** Uses smaller models for fast "System 1" intuitions, invokes larger models for "System 2" reflection when conflicts detected. Reduces API cost by 38.3%-75.1%.

**Relevance:** Shows hybrid approach (fast + slow) is more efficient than pure slow thinking.

---

### Dual-System Adaptive Decision Framework

**[Authors TBD]. (2025).** DSADF: Thinking Fast and Slow for Decision Making. *arXiv preprint arXiv:2505.08189v2*.
http://arxiv.org/pdf/2505.08189v2

**Key finding:** System 1 uses RL agent for fast decisions, System 2 uses VLM for analytical reasoning. Framework balances both systems adaptively.

---

### Neuro-Symbolic Reasoning

**Nye, M., et al. (2021).** Improving Coherence and Consistency in Neural Sequence Models with Dual-System, Neuro-Symbolic Reasoning. *Proceedings of NeurIPS 2021*.
https://proceedings.neurips.cc/paper/2021

**Key finding:** Combines neural (System 1) and symbolic (System 2) reasoning. Shows significant improvements in coherence and consistency.

---

## Working Memory and Cognitive Load

### Working Memory Deficits in LLMs

**[Authors TBD]. (2024).** LLMs do not have human-like working memory. [Research study cited in Perplexity Deep Research]

**Key finding:** Systematic failures across 17 frontier models spanning 4 model families. LLMs exhibit "irrational and contradictory behavior" on working memory tasks.

**Specific metrics:**
- Gemini 2.0 Flash: 85% accuracy baseline → 72% accuracy with 80% extraneous information
- Context saturation: degradation when relevant info drowned by extraneous tokens
- Attentional residue: prior task contexts interfere with current processing

**Implication:** External memory systems (like Mnemex) are architectural requirements, not enhancements.

---

### Cognitive Load and Performance

**[Authors TBD]. (2024).** Context saturation effects in large language models. [Research cited in Perplexity Deep Research]

**Key finding:** Performance degrades systematically under cognitive load. LLMs cannot effectively prioritize and maintain relevant information under high information density.

---

## Inference Scaling and Reasoning

### Scaling Test-Time Computation

**Snell, C., Lee, J., Xu, K., & Kumar, A. (2024).** Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters. *arXiv preprint arXiv:2408.03314v1*.
http://arxiv.org/pdf/2408.03314v1

**Key finding:** Adding more deliberation time (inference tokens) can be more effective than larger models. Validates the "slow down to speed up" principle.

---

### Inference Scaling Laws

**Azerbayev, Z., et al. (2024).** Inference Scaling Laws: An Empirical Analysis of Compute-Optimal Inference. *arXiv preprint arXiv:2408.00724v1*.
http://arxiv.org/pdf/2408.00724v1

**Key finding:** Establishes empirical scaling laws for test-time computation. Longer inference = better reasoning, but with diminishing returns.

---

### OpenAI o1 Model Architecture

**[OpenAI Research Blog]. (2024).** Learning to Reason with LLMs. [Blog post introducing o1 model]

**Key finding:** OpenAI's o1 model uses extended "thinking time" (deliberation tokens) to solve complex problems. Demonstrates practical application of inference scaling.

**Implication:** Industry validation that "forcing deliberation" improves reasoning quality.

---

## Neuropsychological Testing of AI

### Tower of Hanoi Performance

**Vazquez, H. C. (2023).** Artificial Neuropsychology: Are Large Language Models Developing Executive Functions? *arXiv preprint arXiv:2305.04134v2*.
http://arxiv.org/pdf/2305.04134v2

**Method:** Administered Tower of Hanoi task to GPT-3.5 (standard executive function assessment in clinical neuropsychology).

**Result:** "Inhomogeneous results" with poor planning abilities.

---

**Silver, T., et al. (2024).** Modular Agentic Planner (MAP): A Brain-Inspired Architecture for Planning in Large Language Models. *arXiv preprint arXiv:2410.13272v1*.

**Method:** Tested GPT-4 on Tower of Hanoi with and without MAP architecture.

**Results:**
- GPT-4 baseline: 5% success rate
- GPT-4 + MAP: 46% success rate
- Demonstrates architectural intervention can improve executive function

---

## Practical Interventions and Architectures

### Brain-Inspired Modular Planning

**Silver, T., et al. (2024).** Modular Agentic Planner (MAP): A Brain-Inspired Architecture for Planning in Large Language Models. *arXiv preprint arXiv:2410.13272v1*.
http://arxiv.org/pdf/2410.13272v1

**Architecture:** Decomposes planning into specialized modules analogous to prefrontal cortex regions:
- Dorsolateral PFC → Long-term planning
- Ventromedial PFC → Goal management
- Anterior cingulate cortex → Error detection

**Relevance:** Direct parallel to Anastrophex's neuroanatomical inspiration.

---

### Chain-of-Thought Prompting

**Wei, J., et al. (2022).** Chain-of-Thought Prompting Elicits Reasoning in Large Language Models. *Proceedings of NeurIPS 2022*.

**Key finding:** Explicit intermediate reasoning steps improve problem-solving. Foundation for System 2 thinking in LLMs.

---

### Self-Consistency Decoding

**Wang, X., et al. (2023).** Self-Consistency Improves Chain of Thought Reasoning in Language Models. *Proceedings of ICLR 2023*.

**Key finding:** Sampling multiple reasoning paths and selecting the most consistent answer improves reliability.

---

## Foundational Cognitive Science

### Dual-Process Theory

**Kahneman, D. (2011).** *Thinking, Fast and Slow*. Farrar, Straus and Giroux.

**Foundation:** Establishes System 1 (fast, intuitive) vs. System 2 (slow, deliberate) cognitive framework. Foundational theory for understanding reasoning processes.

---

### Executive Function Theory

**Diamond, A. (2013).** Executive functions. *Annual Review of Psychology, 64*, 135-168.

**Key components:** Working memory, inhibitory control, cognitive flexibility. Standard framework for understanding executive dysfunction in ADHD.

---

### Tower of Hanoi as Executive Function Assessment

**Welsh, M. C., Satterlee-Cartmell, T., & Stine, M. (1999).** Towers of Hanoi and London: Contribution of working memory and inhibition to performance. *Brain and Cognition, 41*(2), 231-242.

**Establishes:** Tower of Hanoi as validated assessment tool for planning and executive function.

---

## Additional Sources from Perplexity Deep Research

**Note:** The Perplexity Deep Research query (conducted October 24, 2025) cited 61 sources. The most relevant have been extracted and categorized above. For the complete list of sources, see `/Users/sc/Documents/GitHub/anastrophex/docs/literature-review.md`, section "Full Perplexity Deep Research Report."

---

## Research Gaps and Future Directions

### Identified Gaps

1. **No explicit "AI ADHD" framework in academic literature** (our novel contribution)
2. **Limited real-time intervention systems** (academic focus is on architecture, not runtime monitoring)
3. **Lack of effectiveness tracking** for interventions (no closed-loop learning systems documented)
4. **Minimal cross-pollination** between AI research and clinical ADHD literature

### Future Research Opportunities

1. **Empirical validation:** Test Anastrophex against neuropsychological battery
2. **Comparative studies:** Benchmark against Tower of Hanoi and other executive function tasks
3. **Intervention effectiveness:** Publish data on which reminders work in which contexts
4. **Academic paper:** Position Anastrophex as practical implementation of AI neuropsychology

---

## Citation Guidelines

When citing this bibliography in Anastrophex documentation:

**For academic rigor:**
```
(Vazquez, 2023; Silver et al., 2024)
```

**For technical documentation:**
```
See docs/bibliography.md for full citations
```

**For inline references:**
```
Research shows LLMs exhibit "poor planning abilities" [Vazquez 2023].
```

---

## Maintenance Notes

**Next Updates:**
- Add DOI numbers when available
- Complete author lists for multi-author papers (currently abbreviated)
- Add page numbers for specific quotes
- Include conference proceedings details
- Add abstracts for key papers
- Link to downloaded PDFs once available

**Missing Information:**
- Some arXiv papers couldn't be downloaded (conversion errors: 2410.03170, 2410.02519, 2406.02961)
- Vazquez paper (2305.04134v2) returned 404 error
- Several papers from Perplexity report need full citation details

---

*Bibliography compiled: October 24, 2025*
*Version: 1.0*
*Maintainer: Anastrophex Research Team*
