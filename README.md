# LLMs for Automated Text Analysis in the Digital Humanities: How Useful Are They Really?

The digitization of historical texts is one of the central and at the same time most demanding tasks in the Digital Humanities. The path from a physical document to a machine-readable digital version consists of many coordinated steps: from scanning the pages to OCR analysis (Optical Character Recognition, i.e., automatic character recognition), error correction, normalization, and finally the extraction of relevant information.
The goal of this pipeline is to enable distant reading – an approach in which large text corpora are analyzed automatically without researchers having to manually read every page. This is essential for extensive source collections.

## Digitization Pipeline

(Illustration)

Many of the required steps are complicated, error-prone, and require either specialized technical knowledge or substantial resources. OCR analysis of historical prints is particularly challenging because:
- typefaces vary
- print quality fluctuates
- suitable training data are rarely available
As a result, source-specific adjustments often have to be developed.

## Objective of the Study
Given these challenges, Large Language Models (LLMs) are coming into focus as a flexible alternative. They are optimized for processing natural language and could supplement or replace classical, often rigid methods and simplify the entire process.

My master’s thesis examines the extent to which this theoretical potential can actually be used under realistic conditions.

Three core tasks were investigated:
- OCR analysis: extracting text directly from page scans
- Orthographic normalization of historical texts
- Named Entity Recognition (NER): identifying personal and place names
Two strict constraints applied:
- Only prompt engineering, no model training (Prompt engineering = formulating optimized input instructions for LLMs)
- Use of small models that run on standard consumer hardware (e.g., models up to about 10–12 billion parameters)
These constraints simulate conditions where projects can be carried out even without extensive technical know-how or computational power, as is often the case in the humanities.

## Methodology
The travel journal of Carl Peter Thunberg from the German Text Archive (DTA) served as the dataset. The source exists at several levels of annotation:
- scans
- plain text files
- XML files with lemmas, POS tags, and NER annotations

This allowed realistic simulations of the individual task areas and precise comparisons with ground truth data.

A master JSON file was created to control the experiments, containing both model inputs and reference data for evaluation.

After testing several lightweight models, the following proved most reliable:
- Gemma 3 (12B) – for language tasks
- Gwen 2.5-VL (3B) – vision model for OCR-like tasks
Both ran locally through Ollama and were automated using Python.
A central methodological component was the strict control of output formats using Pydantic, which forced the LLMs to produce consistent, machine-readable JSON.
Because small models can process only limited amounts of tokens, the corpus was processed sentence by sentence. In total, 6,058 model requests were necessary.

## Experiment 1: Orthographic Normalization
**Objective:** convert historical spellings into modern German without altering content.
The model received explicit rules:
- modernize only spelling and typography
- no synonyms or paraphrasing
- do not change proper names
Examples from a second report by the author were provided to clarify the task.

### Results
**Positive**
- followed basic rules well
- correctly modernized outdated characters
- sentence structure and word choice remained largely intact
low error rate

**Negative**
- inconsistent decisions (context loss due to sentence-wise processing)
- unknown terms sometimes overinterpreted
- occasional unnecessary changes despite clear rules

### Conclusion
Under these conditions, LLM-based normalization is not useful. Rule-based tools remain:
- faster
- more consistent
- easier to control

## Experiment 2: OCR Analysis with a Vision LLM
**Objective:** convert historical book pages directly into machine-readable text without classical OCR.

Originally, the workflow was meant to include both scan and OCR output so the model could correct the existing analysis. Small models, however, failed to process both inputs meaningfully. Therefore, only scans were used.

Ten pages with different layouts were tested.

### Results
**Pro**
- some pages recognized almost flawlessly
- text extraction possible without training
- low computational requirements
- very fast processing

**Contra**
complex layouts caused complete failure
unwanted orthographic modernization
model was maxed out by the task, allowing only very short prompts

### Conclusion
Vision LLMs show potential but are:
not reliable enough in this setup
overwhelmed by complex layouts
Larger models or stronger hardware would likely perform much better.

## Experiment 3: Named Entity Recognition (NER)
**Objective:** detect all personal and place names.

NER is crucial for:
- databases
- knowledge graphs
- geographic analyses
- network analyses of historical actors
Challenges include historical names, varying spellings, and ambiguous context.

Because combined extraction of persons and places performed poorly, two separate runs were executed.

Rules were precisely defined (with ICL examples):
- mark only personal names / only place names
- ignore titles, professions, roles
- ignore general terms
Evaluation was done using DTA XML annotations (hits, missing, false positives).

### Results
**Pro**
- very high recall: over 93% of actual names found
**Contra**
- extremely high number of false positives
  -> personal names: 885 incorrect vs. 422 correct
- systematic misinterpretations
  -> place names: Japanese, town, island → falsely treated as proper names
  -> personal names: king, emperor, governor → treated as valid persons
- occasional extreme outliers (hundreds of hallucinated names)

### Conclusion
LLM-based NER is not practical under these conditions. Classical tools (e.g., spaCy) remain:
- faster
- more consistent
- far easier to control

## Overall Result
The thesis arrives at a clear but sobering conclusion:

Lightweight LLMs can contribute to the analysis of historical texts, but under realistic conditions they are neither reliable nor efficient enough to replace classical methods.

### 1. Main Problem: Unreliability and Inefficiency
- high variance
- partly arbitrary decisions
- prompt engineering does not replace training and is not less complex
- problems are shifted, not solved

The usefulness of LLMs must be considered carefully.
More modern tools are not always better. The normalization of historical texts with LLMs takes significantly longer, requires more computational resources, and ultimately does not outperform traditional rule-based normalization tools available online.

### 2. Resource Limitations of Small Models
- actual token limits often unclear
- vision LLMs break easily when tasks become slightly more complex

## Outlook: Where Does the Potential Lie?
Despite their limitations, LLMs offer promising perspectives:

**Tool Integration**
Format validation tools such as Pydantic greatly improve stability.

**Specialized Agents**
Models like Qwen2.5-VL show potential in image handling and format conversion.

**Better Accessibility**
There is a strong need for graphical interfaces so that researchers can use LLM workflows without deep technical knowledge.

## TL;DR
- LLMs show potential for certain tasks in the Digital Humanities
-  Under realistic conditions, however, they remain unreliable, inconsistent, and inefficient. Classical tools continue to be indispensable.
- LLMs become most useful when combined with structured tools and clearly defined output formats (e.g., Pydantic).
