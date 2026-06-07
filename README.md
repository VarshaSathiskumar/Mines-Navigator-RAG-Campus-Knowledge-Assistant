# 🤖 Mines Navigator: Retrieval-Augmented Campus Knowledge Assistant

## 📌 Domain

Mines Navigator is an AI-powered campus knowledge assistant that helps Colorado School of Mines students quickly find answers to academic and campus-life questions. The system leverages a Retrieval-Augmented Generation (RAG) architecture to retrieve CS graduate student academic information, including degree requirements, registration policies, grading systems, graduation procedures, tuition policies, academic regulations, student conduct expectations, and graduate program offerings from official PDF documents. While this information exists across multiple university documents and PDFs, it can be difficult for students to locate the correct policy quickly. This RAG system consolidates these resources into a searchable assistant that provides grounded answers based on official university documents.
The goal of the project is to make valuable student knowledge more accessible through a conversational interface while ensuring responses remain accurate, transparent, and supported by source documents.

> 🚧 **In Progress:** Enhancing the platform with source citations, document traceability, and expanded knowledge sources to improve transparency, answer reliability, and coverage.
> 
## 🏗️ Features

- Retrieval-Augmented Generation (RAG) based question-answering system
- Semantic search over campus-related documents and discussions
- Grounded responses generated only from retrieved sources
- Support for questions related to academics, housing, dining, and student life
- Source-aware responses for improved transparency
- Interactive chat interface for natural language interactions

## 🛠️ Technology Stack

- Python
- Groq API
- ChromaDB
- Sentence Transformers
- Streamlit

---

## Documents

| # | Source | Type | Description | Location |
|---|--------|------|-------------|----------|
| 1 | Mines Mission, Vision and Strategic Planning | PDF | University mission, vision, core values, and strategic planning overview from the President's Office | `documents/Mines Mission, Vision and Strategic Planning - President's Office.pdf` |
| 2 | Colorado School of Mines Catalog | PDF | Official academic catalog covering the role, mission, degree programs, and educational philosophy of Mines | `documents/catalog.pdf` |
| 3 | Computer Science Graduate Programs | PDF | CS department degrees (MS, PhD, Professional MS, Cybersecurity Certificate), admission requirements, and research areas | `documents/cs.pdf` |
| 4 | Academic Regulations | PDF | Graduate school academic regulations including course registration rules, credit level policies, and program requirements | `documents/generalregulations.pdf` |
| 5 | Graduate Grading System | PDF | Graduate grade symbols, incomplete grade policies, satisfactory progress grades, and pass/fail rules | `documents/graduategradingsystem.pdf` |
| 6 | Graduation | PDF | Graduation application deadlines, commencement ceremony eligibility, and checkout procedures | `documents/graduation.pdf` |
| 7 | Graduation Requirements | PDF | Registration requirements for graduation, checkout steps, and links to deadline resources | `documents/graduationrequirements.pdf` |
| 8 | Policies and Procedures | PDF | Student code of conduct, academic integrity policy, misconduct definitions, and campus rules | `documents/policiesandprocedures.pdf` |
| 9 | Registration and Tuition Classification | PDF | Graduate registration rules, full-time credit loads, overload policy, research credit, and leave of absence procedures | `documents/registrationandtuitionclassification.pdf` |
| 10 | Robotics Graduate Programs | PDF | Robotics graduate certificate, MS (thesis and non-thesis), and PhD degrees, admissions, and combined BS+MS program | `documents/robotics.pdf` |
| 11 | The Graduate School | PDF | Overview of Mines graduate programs, unique interdisciplinary offerings, and list of graduate degrees by field | `documents/thegraduateschool.pdf` |
| 12 | Tuition, Fees, and Financial Assistance | PDF | Tuition and fee policies, refund schedules, financial aid, and room and board refund rules | `documents/tuitionfeesfinancialassistance.pdf` |




## Chunking Strategy


**Chunk size:** I will primarily split the PDF documents by **sections and headings** rather than using fixed-size chunks. The corpus consists of official university documents covering graduate policies, academic regulations, degree requirements, grading systems, tuition policies, and graduation procedures. Since these documents are already organized into meaningful sections, each section will be treated as a chunk whenever possible. If a section is very large (more than approximately 1500 characters), it will be further divided into chunks of approximately **800 characters**.

**Overlap:** For large sections that require further splitting, I will use an overlap of **150 characters**. The overlap helps preserve context when important information appears near the boundary between two chunks. For example, a policy may be introduced in one paragraph and explained in detail in the next paragraph. Without overlap, the retriever may return only part of the information needed to answer a question.

**Reasoning:** Section-based chunking is better suited for structured academic documents because users typically ask about specific policies, requirements, or procedures that are already grouped under headings. This approach keeps related information together and improves retrieval accuracy. If a section becomes too large, splitting it into smaller overlapping chunks prevents unrelated content from being grouped together while still preserving enough context for grounded responses. I will evaluate chunk quality by checking whether retrieved chunks represent complete policy sections and contain sufficient information to answer test questions accurately.


---

## Embedding Model

**Embedding model:**
all-MiniLM-L6-v2 via sentence-transformers

**Top-k:**
Retrieving 4 chunks gives the LLM enough context without overwhelming it with unrelated text. If I retrieve too few chunks, the answer may miss important details. If I retrieve too many, the answer may become less focused or include irrelevant information.

**Production tradeoff reflection:**

Semantic search is useful because it can find related content even when the query and document do not use the exact same words. For example, a question about “career help” may retrieve chunks about the Career Center, internships, advising, or resume support.If this were deployed for real users and cost was not a constraint, I would compare stronger embedding models based on retrieval accuracy, context length, latency, cost, and performance on campus-specific language.


---

## Grounded Generation

**System prompt grounding instruction:**
The model receives this instruction at the start of every prompt:
> "Answer the question using ONLY the information in the provided documents below. If the documents do not contain enough information to answer, say: 'I don't have enough information on that.'"
Retrieved chunks are injected as labeled blocks — `[filename] chunk text` — separated by `---`, so the model only sees content from the retrieved documents, nothing else.

**How source attribution is surfaced in the response:**
The model is instructed to end every answer with `Sources: <document name(s)>`, citing only what it used. As a fallback, if the response contains "I don't have enough information", the code in `generate.py` strips the entire response down to just that phrase — removing any sources the model may have hallucinated.

---

## Evaluation Report

<!-- Run your 5 test questions from planning.md through your system and record the results.
     Be honest — a partially accurate or inaccurate result that you explain well is more
     valuable than a suspiciously perfect result. -->

| # | Question | Expected answer | System response (summarized) | Retrieval quality | Response accuracy |
|---|----------|-----------------|------------------------------|-------------------|-------------------|
| 1 | What GPA must graduate students maintain to remain in good academic standing? | The system should reference the Graduate Grading System or Academic Regulations document and explain the minimum GPA requirements for graduate students. | I don't have enough information on that. | Off-target  | Inaccurate
| 2 | What are the degree options available in the Computer Science graduate program? | The system should reference the Computer Science Graduate Programs document and describe the MS, Professional MS, PhD, and certificate options. | The Computer Science graduate program offers the following degree options: 1. MS degree in Computer Science (with thesis or non-thesis option) 2. PhD degree in Computer Science (with sufficient flexibility to prepare candidates for various careers). | Partially relevant | Partial accurate
| 3 | What is considered full-time enrollment for a graduate student? | The system should reference the Registration and Tuition Classification document and explain the credit-hour requirements for full-time status. | To determine full-time enrollment for a graduate student, we need to consider the information provided in the documents. According to the [registrationandtuitionclassification.pdf] document, students enrolled in thesis-based degree programs who have completed a minimum number of courses and research credits can continue to pursue their graduate program at a reduced registration level, but still be considered full-time students. However, the document does not explicitly state what is considered full-time enrollment in terms of credits. Sources: registrationandtuitionclassification.pdf | Partially relevant | Partial accurate
| 4 | What steps are required to apply for graduation? | The system should reference the Graduation or Graduation Requirements document and describe the graduation application and checkout process. | To apply for graduation, students must fill out the appropriate forms for each degree and apply to graduate for each degree. Additionally, students must officially check out of their degree program prior to graduation, and notification of the checkout steps will be sent by the Graduate Office after a student has applied to graduate. Sources: graduation.pdf, graduationrequirements.pdf | Relevant | Accurate
| 5 | What happens if a student receives an incomplete grade? | The system should reference the Graduate Grading System document and explain the policies regarding incomplete grades and their resolution. | An Incomplete (INC) is a temporary grade given to a student when illness, necessary absence, or other reasons beyond the student's control prevent completion of course requirements by the end of the academic term. This grade is restricted to cases in which the student satisfactorily completed a significant amount of the coursework, including attendance and participation. Sources: graduategradingsystem.pdf | Relevant | Accurate


**Retrieval quality:** Relevant / Partially relevant / Off-target  
**Response accuracy:** Accurate / Partially accurate / Inaccurate

---

## Failure Case Analysis

<!-- Identify at least one question where retrieval or generation did not work as expected.
     Write a specific explanation of *why* it failed, tied to a part of the pipeline.

     "The answer was wrong" is not an explanation.

     "The relevant information was split across a chunk boundary, so retrieval returned
     only half the context — the model didn't have enough to answer correctly" is an explanation.

     "The embedding model treated the professor's nickname as out-of-vocabulary and returned
     results from an unrelated review" is an explanation. -->

**Question that failed:**

What GPA must graduate students maintain to remain in good academic standing?

**What the system returned:**

"I don't have enough information on that."

**Root cause (tied to a specific pipeline stage):**

The failure is at the ingestion stage. The GPA requirement for good academic standing is defined in the Graduate Grading System document, but the relevant section was either split across chunk boundaries or the heading-based chunker isolated it as a short fragment without enough surrounding context. The retriever returned chunks that mentioned grades and symbols but not the specific GPA threshold, so the LLM correctly refused to answer rather than guess.

**What you would change to fix it:**

Add more targeted source documents — specifically the Graduate School's academic standing policy page or the full graduate catalog section on satisfactory academic progress. These documents explicitly state the minimum GPA (3.0) required for good standing. Additionally, reviewing chunk boundaries around the grading system section would help ensure the GPA rule and its consequences are not split across separate chunks, making it more likely the retriever surfaces the right content for this type of question.

---

## Spec Reflection

<!-- Reflect on how planning.md shaped your implementation.
     Answer both questions with at least 2–3 sentences each. -->

**One way the spec helped you during implementation:**

The chunking strategy in the spec — section-based splitting with a 1500-character ceiling — directly shaped how chunk.py was built. Having that decision documented meant the implementation had a clear target rather than defaulting to arbitrary fixed-size chunks.

**One way your implementation diverged from the spec, and why:**

The spec listed web URLs as sources. In practice, three of those URLs returned 404 errors and Reddit blocked scraping entirely, so the implementation pivoted to local PDF documents exclusively. The PDFs turned out to be more reliable and content-rich than the web pages anyway.

---

## AI Usage

### Instance 1

- **What I gave the AI:**
  
  I provided my Documents section, chunking strategy, and architecture diagram. I explained that my knowledge base consists of Colorado School of Mines graduate program and policy PDFs and asked the AI to generate a document ingestion and chunking pipeline.

- **What it produced:**
  
  The AI generated a Python script that loaded documents, extracted text, cleaned the content, and split documents into fixed-size chunks of 800 characters with a 150-character overlap.

- **What I changed or overrode:**
  
  After reviewing the generated solution, I realized that my documents are highly structured academic PDFs organized by sections and headings. Instead of relying entirely on fixed-size chunking, I changed the strategy to use section-based chunking whenever possible and only apply 800-character chunks with overlap when a section becomes too large. This preserves complete policies and improves retrieval quality.

### Instance 2

- **What I gave the AI:**
  
  I provided the list of PDF documents and asked the AI to help design evaluation questions and expected answers that would accurately test the RAG system.

- **What it produced:**
  
  The AI initially generated evaluation questions based on university webpages, including campus dining, student resources, and contact directories.

- **What I changed or overrode:**
  
  Since my final corpus contains only graduate-school PDF documents, I replaced those questions with policy-focused questions related to graduate grading requirements, degree programs, registration rules, graduation procedures, and incomplete grade policies. This ensured that the evaluation matched the actual content available in the knowledge base and could be objectively verified against the source documents.
