---
name: academic-researcher-writing
description: End-to-end support for academic research AND scholarly writing — use this skill
  whenever the user mentions research papers, literature reviews, thesis writing,
  dissertations, journal articles, grant proposals, peer review responses, conference
  abstracts, IMRaD structure, citations, research methodology, ArXiv, PubMed, academic
  databases, or scholarly sources. Also trigger for "help me research X topic",
  "write my abstract", "structure my paper", "respond to reviewers", "find sources for",
  "synthesize literature on", or any task that combines finding information with producing
  scholarly output. This skill merges agentic deep-research capabilities with production-
  quality academic writing workflows.
  metadata:
  authors: "Ziad Elnagar"
---

# Academic Researcher & Writer

A unified skill for the full academic pipeline: **find → synthesize → write → refine**.

Covers two phases that often overlap:

1. **Research Phase** — query formulation, source discovery, RAG-style synthesis, gap analysis
2. **Writing Phase** — structure selection, drafting, citation formatting, peer review response

---

## When to Use Which Module

| User says… | Go to |
|---|---|
| "Find papers on X", "what does the literature say about Y" | → [Research Phase](#research-phase) |
| "Help me write my intro / lit review / methods" | → [Writing Phase](#writing-phase) |
| "Respond to reviewer comments" | → [Peer Review Response](#peer-review-response) |
| "Write a grant proposal / abstract / thesis chapter" | → [Writing Phase](#writing-phase) |
| "I need to research AND write about X" | → Do both phases in sequence |

---

## Research Phase

### Step 1 — Clarify the Research Question

Before searching, pin down:
- **Topic + scope**: What domain? What time frame?
- **Question type**: Empirical ("does X cause Y?"), theoretical ("how does X explain Y?"), methodological ("what methods are used to study X?"), or applied ("how is X used in practice?")
- **Depth**: Quick overview vs. comprehensive systematic review
- **Output format**: Summary, annotated bibliography, synthesis table, or draft section

### Step 2 — Query Formulation

Translate user intent into effective search strings:

```
Boolean logic:  (machine learning OR deep learning) AND (climate change OR global warming)
Field-specific: ("neural network" AND "interpretability") NOT survey
Date-bounded:   after:2020 before:2025
```

**Key databases by discipline:**

| Field | Primary | Secondary |
|---|---|---|
| CS / AI / ML | ArXiv, Semantic Scholar | ACM DL, IEEE Xplore |
| Life sciences | PubMed / MEDLINE | bioRxiv, PLoS |
| Social sciences | SSRN, JSTOR | Google Scholar |
| All fields | Google Scholar | Semantic Scholar |
| Open access | OpenAlex | CORE, Unpaywall |

### Step 3 — Source Evaluation (SIFT + CRAAP)

For each source, check:
- **Currency**: Publication date, last update
- **Relevance**: Directly addresses the question?
- **Authority**: Peer-reviewed? Author credentials? Journal impact factor?
- **Accuracy**: Methodology sound? Data available? Reproducible?
- **Purpose**: Research vs. opinion vs. advocacy?

Citation counts and forward citations (who cites this paper?) are strong relevance signals.

### Step 4 — Synthesis

Don't summarize papers one-by-one. Instead:

1. **Theme map**: Group papers by argument, finding, or method
2. **Tension detection**: Where do sources agree? Where do they conflict?
3. **Gap identification**: What question is unanswered? What population/context is missing?
4. **Timeline**: How has consensus evolved?

Output a **synthesis matrix** if the user has 5+ sources:

```
| Source | Method | Sample/Context | Key Finding | Limitations |
|--------|--------|----------------|-------------|-------------|
| ...    | ...    | ...            | ...         | ...         |
```

---

## Writing Phase

### Document Type Selection

| Task | Structure |
|---|---|
| Empirical paper | IMRaD (Intro, Methods, Results, Discussion) |
| Review / survey | Intro → Scope → Themes → Gaps → Conclusion |
| Theoretical paper | Problem → Framework → Analysis → Implications |
| Grant proposal | Specific Aims → Background → Approach → Significance |
| Conference abstract | Background → Objective → Methods → Results → Conclusion (150–300 words) |
| Thesis chapter | Chapter intro → lit → methods → findings → summary |

### IMRaD Deep Dive

**Introduction** — build the "inverted triangle":
1. Broad context (why does this field matter?)
2. Specific gap or problem (what is not yet known?)
3. Study rationale (why this approach?)
4. Research question / hypothesis (explicit statement)
5. Paper overview (optional 1–2 sentences)

**Methods** — enough to replicate:
- Participants / data sources (sampling, inclusion/exclusion criteria)
- Procedure / instruments
- Analysis approach (statistical tests, models, frameworks)
- Ethical considerations if applicable

**Results** — present, don't interpret:
- Lead with the answer to each research question
- Tables and figures before prose discussion of them
- Negative/null results deserve equal reporting

**Discussion** — interpret and contextualize:
1. Restate key findings (briefly)
2. Relate to prior literature (support or contradict?)
3. Explain unexpected findings
4. Acknowledge limitations honestly
5. Implications (theoretical and/or practical)
6. Future work
7. Conclusion paragraph

### Literature Review (Standalone)

Structure:
```
1. Introduction (scope + purpose)
2. Search strategy (transparent and reproducible)
3. Thematic sections (not paper-by-paper summaries)
4. Critical analysis (tensions, gaps, methodological issues)
5. Conclusion + research agenda
```

**Rules:**
- Every paragraph should advance an argument, not just report
- Use topic sentences that make a claim; then support with citations
- Avoid "Smith (2020) found that…" as a paragraph opener — lead with the idea

### Grant Proposal

Read the call FIRST. Then:

**Specific Aims** (1 page — most important):
- Hook sentence (the problem)
- Long-term goal
- Objective of this grant
- Central hypothesis
- 3–4 specific aims (verb + measurable outcome)
- Innovation + significance statement

**Research Strategy**:
- Significance: Why now? What changes if successful?
- Innovation: What's new about your approach?
- Approach: Aim-by-aim with methods, expected outcomes, potential problems, alternatives

**Common mistakes to avoid:**
- Vague aims ("we will study X") → make them testable
- No preliminary data → include even small pilot results
- Ignoring reviewer perspective → answer "so what?" constantly

---

## Peer Review Response

### General Strategy

Reviewers want to see:
1. You read every comment carefully
2. You either fixed it or have a principled reason not to
3. Changes are easy to find in the manuscript

### Response Structure

```
Dear Editor and Reviewers,

Thank you for the thorough and constructive feedback on [title]. 
We have addressed all comments and summarize our responses below.

---

REVIEWER 1

Comment 1: [Quote reviewer comment verbatim]
Response: [Your response — direct and specific]
Manuscript change: [Where/what changed, or "No change — see rationale below"]

Comment 2: ...
```

### Tone Rules
- Never defensive, never dismissive
- "We agree that..." or "We respectfully disagree because..." — both are valid
- If you disagree, provide evidence or logical argument
- Acknowledge when a comment improved the paper

---

## Citation & Style Quick Reference

| Style | Common in | Key feature |
|---|---|---|
| APA 7 | Social/behavioral sciences, education | Author-date in text |
| MLA 9 | Humanities | Author-page in text |
| Chicago / Turabian | History, some humanities | Footnotes or author-date |
| Vancouver | Medicine, biomedical | Numbered references |
| IEEE | Engineering, CS | Numbered, bracketed |
| Harvard | Business, sciences (UK) | Author-date variant |

For AI/ML papers: Use the citation style of the target venue (NeurIPS, ICML = custom BibTeX; ACL = ACL anthology format).

---

## Output Quality Checklist

Before finalizing any piece of academic writing, verify:

- [ ] Research question is explicit and answerable
- [ ] Claims are supported by cited evidence
- [ ] Methods are described reproducibly
- [ ] Limitations are honestly stated
- [ ] Hedging language used where appropriate ("suggests", "may", "appears to")
- [ ] Abstract stands alone (no undefined acronyms, no citations)
- [ ] Correct citation style applied consistently
- [ ] Passive vs. active voice matches field conventions
- [ ] Word count within target range
- [ ] Figures/tables have captions that stand alone

---

## Reference Files

- `references/research-databases.md` — Full database guide with access tips
- `references/writing-patterns.md` — Sentence-level patterns for each IMRaD section
- `templates/abstract-template.md` — Structured abstract for 5 common formats
- `templates/peer-review-response.md` — Full response letter template
- `templates/grant-aims-page.md` — NIH-style specific aims template

---

## Quick Prompts (Copy-Ready)

**To kick off research:**
> "I'm researching [topic]. My research question is [RQ]. Target audience: [journal/field]. I need [overview / comprehensive lit review / specific papers on subtopic]. Please start with Step 1 of the Research Phase."

**To kick off writing:**
> "I'm writing a [paper type] about [topic] for [venue/course]. I have [sources / no sources yet]. Please guide me through the Writing Phase starting with structure selection."

**To get peer review help:**
> "I have reviewer comments for my paper on [topic]. I'll paste the comments — please help me write a professional, point-by-point response letter."