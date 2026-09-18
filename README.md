# 🤖 Mines Navigator: Retrieval-Augmented Campus Knowledge Assistant

## 📌 Domain

Mines Navigator is an AI-powered campus knowledge assistant that helps Colorado School of Mines students quickly find answers to academic and campus-life questions. The system leverages a Retrieval-Augmented Generation (RAG) architecture to retrieve CS graduate student academic information, including degree requirements, registration policies, grading systems, graduation procedures, tuition policies, academic regulations, student conduct expectations, and graduate program offerings from official PDF documents. While this information exists across multiple university documents and PDFs, it can be difficult for students to locate the correct policy quickly. This RAG system consolidates these resources into a searchable assistant that provides grounded answers based on official university documents.
The goal of the project is to make valuable student knowledge more accessible through a conversational interface while ensuring responses remain accurate, transparent, and supported by source documents..

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
