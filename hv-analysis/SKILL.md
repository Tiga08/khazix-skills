---
name: hv-analysis
description: |
  Horizontal-Vertical Analysis — a deep research skill created by digital life Khazix. Combines Saussure's diachronic-synchronic analysis, longitudinal-cross-sectional research design from social science, business school case study methods, and competitive strategy analysis.
  Use when a user wants to systematically research a product, company, concept, technology, or person. The core is dual-axis analysis: the vertical axis traces the full lifecycle from origin to present (told as narrative), the horizontal axis runs a systematic comparison with competitors/peers on the current time-slice, and the cross-axis intersection produces unique insights. The final deliverable is a beautifully typeset PDF research report.
  Chinese triggers: 横纵分析、研究一下、帮我分析、深度研究、做个研究、调研一下、竞品分析、帮我看看这个东西怎么样、这个产品/公司/概念是怎么回事、帮我摸清楚、帮我搞懂、帮我做个deep research。
  Also triggers on phrases like "help me understand X" or "what's the story behind X" when the context implies systematic deep research rather than a simple definition. Suitable when a user drops a product name, company name, or technical term and says "research this for me."
  Do NOT use for simple definitions ("what is X?"), WeChat article writing (use khazix-writer), or title/summary generation (use wechat-title).
---

# Horizontal-Vertical Deep Research

> **Methodology Origins**
> Horizontal-Vertical Analysis was developed by digital life Khazix, synthesizing diachronic-synchronic analysis from linguistics (Saussure), longitudinal-cross-sectional research design from social science, business school case study methodology, and the core ideas of competitive strategy analysis into a general-purpose research framework for products, companies, concepts, and people. The core principle never changes: the vertical axis pursues temporal depth, the horizontal axis pursues contemporary breadth, and the intersection yields judgment.

You are conducting a Horizontal-Vertical deep research session. The final deliverable is a **beautifully typeset PDF research report**.

## Prerequisites

### Environment Setup

1. **Confirm the PDF conversion script is available**: This skill ships with `scripts/md_to_pdf.py` (WeasyPrint-based), which converts the final Markdown report into a well-typeset PDF. Make sure dependencies are installed: `pip install weasyprint markdown --break-system-packages`.
2. **Writing style**: This skill has a complete writing style guide built in (see "Writing Style" below) — no need to load other skills.

### Define the Research Subject

After receiving the user's input, confirm the following. If the user has been sufficiently specific (e.g. "do an HV analysis of Hermes Agent"), skip the questions and start immediately:

1. **Research subject**: The specific product/company/concept/person name
2. **Type**: Product, company, concept, person, or other?
3. **Research motivation** (optional): Why research it? What happened recently?
4. **Special focus** (optional): Any direction you want to go deeper on?

---

## Step 1: Online Information Gathering

The quality of this methodology depends entirely on the richness and accuracy of the information. **You must search online** — do not rely solely on existing knowledge. The value of a research report lies in depth and completeness, so err on the side of over-searching rather than letting shallow information hollow out the analysis.

### Parallel Search Strategy

Use sub-agents to search in parallel for efficiency. Recommended division of labor:

- **Sub-Agent 1 — Vertical information**: Origin of the subject, founder background, development history, key events, version iterations, funding, strategic pivots, crises
- **Sub-Agent 2 — Horizontal information**: Competitor identification, each competitor's features and user reputation, industry comparison reviews, market share
- **Sub-Agent 3** (only for complex subjects): Supplementary information — founder deep background, industry environment changes, user community discussions (GitHub Issues, Reddit, Twitter/X, 知乎, etc.)

**Sub-agent web tool usage guide** (include verbatim in each sub-agent's prompt):

Each sub-agent prompt must contain the following web access instructions:

> You need to fetch information from the web. Use the following tools:
> - **WebSearch**: For discovering information sources, getting summaries and keyword results
> - **WebFetch**: When you know a specific URL, use it to extract content from the page
> - If the user's environment has the web-access skill installed (check whether `/mnt/.claude/skills/web-access/SKILL.md` exists), load it first and follow its guidance — it provides stronger browser CDP capabilities
> - Search strategy: Use WebSearch first to discover sources and leads, then WebFetch to extract details from specific URLs
> - Search multiple times with multiple keyword combinations — don't give up after one search
> - Primary sources beat secondary sources: official blog > authoritative original reporting > reposts/aggregators
> - **Academic subjects must check arXiv**: If the subject involves academic concepts, algorithms, AI models, or technical paradigms, you must query arXiv for relevant papers. Call: `curl -s "https://export.arxiv.org/api/query?search_query=all:keyword1+AND+all:keyword2&max_results=10"`, or use WebFetch on the same URL. Returns XML with titles, authors, abstracts, publication dates, and PDF links. Adjust keyword combinations and result counts as needed. After finding key papers, use WebFetch on the paper page (`https://arxiv.org/abs/<paper-id>`) for more details.

Describe objectives in the prompt ("obtain," "investigate," "learn about"), not verbs that imply specific methods ("search," "crawl") — let the sub-agent decide the best approach.

### Information Source Priority

Primary sources beat secondary sources — multiple outlets citing the same error creates a circular-corroboration illusion:

| Information Type | Primary Sources |
|---------|---------|
| Product updates / tech decisions | Official blog, GitHub Release Notes, founder tweets |
| Funding / business data | Official company announcements, SEC / corporate filings |
| User reputation | GitHub Issues, Reddit discussions, Twitter/X, 知乎 posts |
| Industry analysis | Authoritative original reporting (not reposts) |
| Academic / technical principles | arXiv papers (`export.arxiv.org/api/query`), Google Scholar, conference proceedings |

### Information Sufficiency Self-Check

After searching, verify:
- Vertical: Can you tell a complete story? Are there obvious information gaps?
- Horizontal: Is the competitor list complete? Any major players missing? Enough information on each competitor for comparison?
- Sources: Are key facts backed by reliable sources? Any judgments resting on a single source?

If information is insufficient, search more. Don't settle.

---

## Step 2: Vertical Analysis (Diachronic / Longitudinal)

Trace the full arc of the research subject's development from birth to the present along the timeline. This is the main body of the report and should be the longest section.

### Content Requirements

**Origin tracing**: What was the context of its birth? What technology/philosophy/need did it spring from? Who were the founding team or core drivers? What had they done before, and why were they the ones to do this? What was the industry environment like at the time? Was there a key event or inspiration that directly sparked its creation?

**Birth node**: The definitive first release/founding/proposal date, the initial form and positioning, and how it differs from today.

**Evolution arc**: From birth to present, lay out all key milestones in chronological order. Including but not limited to: major version updates, funding events, team changes, strategic pivots, technical architecture shifts, user-scale milestones, major partnerships or acquisitions, PR crises or controversies.

**Decision logic**: At each key node, reconstruct the reasoning behind the decision as much as possible. Why choose A over B? What constraints existed at the time? Which early decisions "locked in" later directions and became hard to reverse? What mechanisms drove ever-deeper commitment (network effects, ecosystem lock-in, tech-stack choices, etc.)?

**Phase breakdown**: Divide the full arc into natural phases (nascent, rapid growth, transformation, etc.), each with a defining characteristic and a central tension.

### Length

6,000–15,000 words. Subjects with longer histories and more milestones should approach the upper bound; newer subjects the lower bound. The core principle is to tell the story fully and deeply — every key milestone deserves elaboration. Don't skip important details for the sake of brevity. Better to write long and rich than to skim the surface.

---

## Step 3: Horizontal Analysis (Synchronic / Cross-Sectional)

Take the current point in time as the cross-section and compare the research subject comprehensively against competitors/peers in the same space.

### First, Assess the Competitive Landscape

Handle three scenarios:

**Scenario A: No direct competitors.** If the subject is a brand-new category or occupies an extremely strong monopoly position, skip individual comparisons. Instead analyze: Why are there no competitors? Is the category too new, the moat too high, or the market too small? Where is the most likely direction for competitors to emerge? Are there indirect substitutes or previous-generation solutions to benchmark against?

**Scenario B: Few competitors (1–2).** Do an in-depth comparison for each, with detailed analysis per competitor.

**Scenario C: Ample competitors (3+).** Select the 3–5 most representative for detailed comparison; mention the rest briefly.

### Comparison Dimensions

Adjust flexibly based on the type of research subject, but cover at least the following:

**Core differentiators**: Technical approach / core methodology / underlying logic, product form / business model / organizational structure, target users / audience / applicable scenarios, core strengths and obvious weaknesses, pricing strategy / resource investment / scale.

**User perspective**: What is the real-world user reputation for each competitor? What are the most frequently praised strengths and criticized pain points in community reviews and usage experiences? Is there a gap between how users actually use the product and the official positioning? Don't write the comparison as a prose version of a spec-sheet — explain what each competitor "lives like" in practice and why users genuinely choose it.

**Niche analysis**: In the overall map of the space, what position does the research subject occupy? What gap does it fill, or who is it competing head-to-head with? Is the current landscape a free-for-all, a duopoly, or a monopoly?

**Trend assessment**: Based on horizontal comparison, what is the trajectory of the research subject within the competitive landscape? What are the opportunities and risks?

### Length

3,000–10,000 words. Scenario A should stay around 3,000 words; Scenario C should devote at least 1,500 words of independent analysis per major competitor — don't gloss over them.

---

## Step 4: Cross-Axis Insights

This is the crown jewel of the report. Weave together the vertical development arc and horizontal competitive landscape to produce integrated, original judgments. Do not write a condensed summary of the preceding sections.

Core questions to answer:

1. **How history shaped the current competitive position**: Which decisions and events in the vertical arc determined where the subject sits in today's horizontal comparison?
2. **Vertical comparison of competitors**: If you place the major competitors on a timeline too, how do their origins and evolution paths differ? How do those differences explain their current characteristics?
3. **Historical roots of today's strengths**: Each current core strength — which historical node or decision can it be traced back to?
4. **Historical roots of today's weaknesses**: Each current core weakness — which historical decision can it be traced back to? Did any "good decisions" of the past become today's baggage?
5. **Future projection**: Based on vertical trends and horizontal competition, lay out three scenarios — the most likely, the most dangerous, and the most optimistic. Each must have logical support.

### Length

1,500–3,000 words.

---

## Writing Style

This is not a cold consulting report — it's a deep research piece that people will actually read start to finish. The writing style needs to balance "research-report rigor" with "Khazix-level readability."

### Core Style Elements Borrowed from Khazix

The following style elements apply directly to report writing (for detailed definitions see the khazix-writer skill):

**Rhythm**: Vary sentence length — short then long, paragraphs that breathe. Don't make every paragraph the same length; a single-sentence paragraph for weight is a valid move. Good rhythm undulates, each time drifting slightly off the main thread before a "callback sentence" pulls you back.

**Narrative-driven, not list-driven**: The vertical section must have a story arc — setup, development, turning point, resolution. For example, why a product suddenly exploded at a particular moment, what the buildup was, what the pivot was. Don't write "January 2023: released A. March 2023: released B" — that's a timeline, not a story.

**Knowledge drops naturally in conversation**: Weave background knowledge into the narrative organically, don't announce "now let me explain the concept of..."

**Make bold judgments**: Offer opinions and insights, but every opinion must have factual support. Facts first, judgment second. If it's speculation, label it explicitly. Frame judgments as "my read is..." or "I'd argue..." — acknowledging subjectivity rather than issuing authoritative pronouncements.

**Peel the onion**: Don't state the conclusion upfront. Unfold through "phenomenon → surface explanation → deeper question → core insight." Let the reader participate in the thinking process.

**Cultural elevation**: In the cross-axis insights section, connect to broader cultural/philosophical/historical reference points. Not forced thematic uplift — the kind where you're telling a story and it naturally reminds you of something bigger.

**Callback loops**: Details and hooks planted early in the vertical section should callback in the cross-axis insights or conclusion. The sense of causal closure is what turns a report from an "information stream" into a "piece of work."

### Elements Unsuitable for Research Reports

The following elements work for blog-style articles but should be restrained in research reports:

- **Excessive informality**: The report can have a conversational feel, but don't saturate it with slang and throwaway expressions. An occasional colloquial touch is fine, but at a much lower density than a blog post.
- **Removing all subheadings**: Blog articles aim for a single unbroken flow without subheadings. Research reports are different — 10,000–30,000 words without clear structure and navigation will lose the reader. Reports need clear chapter structure.
- **Punctuation constraints can be relaxed**: Blog-style articles restrict colons and em-dashes. Research reports can use them normally, because informational efficiency matters here.
- **Fixed footer**: Don't add blog-style subscription/share CTAs at the end.

### Absolute No-Go Zone

The following AI-flavored markers must be avoided regardless of genre:

- Crutch transitions: "First... Second... Finally...", "In conclusion", "To summarize", "All things considered"
- Hollow hedging: "It's worth noting that", "Notably", "It should be mentioned that", "Needless to say"
- Empty buzzwords: "empower", "leverage", "unlock", "key lever/driver", "end-to-end ecosystem", "holistic approach", "synergy"
- Template openings: "In today's rapidly evolving AI landscape", "As technology continues to advance", "In an era of unprecedented change"
- Filler phrases: "Simply put", "Essentially", "In other words", "It goes without saying"
- Hype words (when used as filler): "game-changer", "paradigm shift", "robust ecosystem", "deep dive", "next-generation", "cutting-edge"
- Vague tool names: Don't write "an AI tool" or "a certain model" — use the specific name
- Fabricated scenarios: If you can't find a piece of information, honestly label it "information not available" — never fabricate

### Write Like a Human

Avoid consulting-speak and empty generalizations. Replace sweeping statements with specific details and examples. Don't write "the company achieved rapid growth during this phase" — write "from $10M ARR in mid-2024 to $1B by late 2025, the growth curve was almost vertical."

---

## Step 5: Generate the PDF Report

After the report is written, use this skill's built-in `scripts/md_to_pdf.py` to convert the Markdown to a beautifully typeset PDF.

### Conversion Workflow

1. **Complete the Markdown draft first**: Write the full report in standard Markdown format, saved as `[subject]_hv-analysis-report.md`
2. **Install dependencies** (if not already): `pip install weasyprint markdown --break-system-packages`
3. **Run the conversion script**:
   ```bash
   python [skill-directory]/scripts/md_to_pdf.py input.md output.pdf --title "Subject Name" --author "Khazix"
   ```
4. The script automatically generates an intermediate HTML file (for debugging) and the final PDF

### Built-in Typesetting

`md_to_pdf.py` includes a complete CSS typesetting scheme — no manual adjustments needed:

- **Page**: A4, margins top 25mm / left-right 20mm / bottom 20mm
- **Cover page**: Auto-generated with title (28pt dark blue), subtitle "Horizontal-Vertical Analysis Deep Research Report", author info, decorative divider
- **Colors**: H1 = #1a5276 dark blue, H2 = #1e8449 green, H3 = #2e86c1 light blue, H4 = #5b2c6f purple, body = #2c3e50 dark gray
- **Fonts**: CSS fallback chain `"Droid Sans Fallback", Helvetica, Arial, sans-serif`, handles mixed CJK/Latin text automatically
- **Body text**: 10.5pt, line-height 1.75, justified, orphan/widow control
- **Blockquotes**: Left 3pt dark blue border + light gray background
- **Tables**: Full-width, dark blue header with white text, zebra-striped rows
- **Header**: `{title} | Horizontal-Vertical Deep Research` (hidden on first page)
- **Footer**: `Page X` (hidden on first page)
- The first H1 in the Markdown is automatically extracted for the cover title and will not repeat in the body

### Markdown Writing Tips

For best PDF output from the script:

- Use `# Title` on the first line as the report title (auto-used for the cover)
- Immediately after the title, you can write a metadata line in blockquote format: `> Research date: ... | Domain: ... | Subject type: ...` — this will be extracted to the cover
- Use `##` for major chapter headings (Vertical Analysis, Horizontal Analysis, Cross-Axis Insights, etc.)
- Use `###` and `####` for subsections
- Tables use standard Markdown table syntax
- Quotes use `>` syntax
- Bold uses `**text**`

### End Matter

At the end of the Markdown draft, include:
- **Sources**: A complete list of all cited sources, with URLs and access dates
- **Methodology note**: A brief note on the origin of HV Analysis (1–2 sentences is enough)

### Report Structure Template

```
Cover Page

Table of Contents

I. One-Line Definition
[One sentence that says exactly what this thing is]

II. Vertical Analysis: From Origin to Present
[Full vertical narrative, 6,000–15,000 words]

III. Horizontal Analysis: Competitive Landscape
[Horizontal comparison analysis, 3,000–10,000 words]

IV. Cross-Axis Insights
[Cross-analysis and future projection, 1,500–3,000 words]

V. Sources
[Complete list of cited sources]
```

### File Naming and Delivery

Name the PDF `[subject-name]_hv-analysis-report.pdf` and save it to the user's working directory.

---

## Adapting to Different Research Subject Types

The core principle stays the same (vertical for temporal depth, horizontal for contemporary breadth), but emphasis shifts:

**Researching a product**: Vertical axis focuses on version iterations, technical roadmap evolution, user growth curve, key product decisions; horizontal axis focuses on feature comparison, performance comparison, user experience, pricing.

**Researching a company**: Vertical axis focuses on founding team, funding history, strategic pivots, organizational changes, key personnel moves; horizontal axis focuses on business model differences, market share, revenue comparison, organizational structure differences.

**Researching a concept** (technical paradigm, business model, cultural phenomenon): Vertical axis focuses on the concept's origin (who proposed it, based on what theory/need), how it gained traction, what debates and evolutions it went through; horizontal axis focuses on distinctions from adjacent concepts, applicable scenarios for each, arguments from different camps.

**Researching a person**: Vertical axis focuses on personal history, career trajectory, key decisions, growth arc, evolution of public statements; horizontal axis focuses on comparison with peers in the same field (working style, approach, achievements, influence, divergent choices).

---

## Length Overview

| Section | Word Count Range | Notes |
|-----|---------|------|
| Vertical Analysis | 6,000–15,000 words | Report backbone — don't skim the surface |
| Horizontal Analysis | 3,000–10,000 words | Adjust based on competitor count |
| Cross-Axis Insights | 1,500–3,000 words | Crown jewel — deliver original judgments |
| **Full Report** | **10,000–30,000 words** | Don't fear length — depth and completeness are the value |

---

## Quality Checklist

Self-check before delivery:

- [ ] Is the vertical axis written as narrative prose? Does it read with causal logic and historical context? Not a chronological bullet list?
- [ ] Is the founder/initiator background and motivation explored with sufficient depth?
- [ ] Is every key milestone fully developed, with no important details skipped for brevity?
- [ ] Is decision logic reconstructed? Not just "what happened" but "why they chose this"?
- [ ] Is the horizontal axis scenario correctly identified (A/B/C)? Is the competitor analysis deep enough?
- [ ] Does the user reputation section cite real user voices? Not just official marketing?
- [ ] Do the cross-axis insights produce new judgments, not a condensed version of prior sections?
- [ ] Do all three future scenarios have logical support?
- [ ] Does the writing have rhythm and readability? Not a cold consulting report?
- [ ] Zero violations from the Absolute No-Go Zone?
- [ ] Are all key facts attributed to information sources?
- [ ] Is missing information honestly labeled "not available" rather than fabricated?
- [ ] Is the PDF well-typeset, clearly structured, and readable?
- [ ] Is total word count within the 10,000–30,000 range?
