# Mines Navigator: Retrieval-Augmented Campus Knowledge Assistant

## Overview

Mines Navigator is an AI-powered campus knowledge assistant that helps Colorado School of Mines students quickly find answers to academic and campus-life questions. The system leverages a Retrieval-Augmented Generation (RAG) architecture to retrieve relevant information from student-generated resources such as course reviews, professor feedback, housing discussions, campus guides, and community forums, and generates responses grounded in the retrieved content.

The goal of the project is to make valuable student knowledge more accessible through a conversational interface while ensuring responses remain accurate, transparent, and supported by source documents.

## Features

- Retrieval-Augmented Generation (RAG) based question-answering system
- Semantic search over campus-related documents and discussions
- Grounded responses generated only from retrieved sources
- Support for questions related to academics, housing, dining, and student life
- Source-aware responses for improved transparency
- Interactive chat interface for natural language interactions

## Technology Stack

- Python
- Groq API
- ChromaDB
- Sentence Transformers
- Gradio

## Example Questions

- Which professors are recommended for Applied Algorithms?
- What are the best off-campus housing options near Mines?
- Where do graduate students usually study on campus?
- What advice do students have for incoming freshmen?
- Which dining options are most popular among students?

## Project Workflow

1. Collect student-generated content from community resources.
2. Split documents into smaller text chunks.
3. Generate embeddings for each chunk using a sentence transformer model.
4. Store embeddings in ChromaDB for efficient semantic retrieval.
5. Retrieve the most relevant chunks for a user's question.
6. Use a Groq-hosted LLM to generate a grounded response based on the retrieved context.
7. Return an answer along with the relevant source information.

## Learning Outcomes

This project demonstrates practical applications of:

- Retrieval-Augmented Generation (RAG)
- Vector databases and semantic search
- Prompt engineering
- Large Language Model integration
- Information retrieval and knowledge grounding
- Conversational AI system development

## Future Enhancements

- Add citation links to original sources
- Support document uploads for custom knowledge bases
- Implement conversation memory
- Add source ranking and confidence scoring
- Expand coverage to additional university resources
