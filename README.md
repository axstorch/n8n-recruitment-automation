# AI-Powered Resume Evaluation Automation (n8n)

## Overview

This project is an **end-to-end resume screening automation** built using **n8n**.  
It listens for incoming resumes, prevents duplicate candidate entries, parses resume content, and evaluates candidates against a given **Job Description (JD)** using **OpenAI**.

The focus of this project is **reliability and real-world robustness**, not just automation. It is designed to handle common edge cases such as duplicate resumes, broken data flow, and AI-generated output inconsistencies.

---

##  Problem Statement

Manual resume screening is:
- Time-consuming
- Difficult to scale
- Inconsistent across evaluators
- Prone to duplicate candidates

On the automation side, many workflows fail because:
- Files are uploaded multiple times
- AI nodes overwrite or drop fields
- Binary nodes break JSON continuity
- Merge nodes silently discard data

This project solves both **hiring inefficiency** and **automation fragility**.

---

## Features

- **Automated Resume Intake**  
  Automatically listens for resume uploads.

- **Deduplication System**
  - Email-based candidate deduplication
  - Hash-based file deduplication to prevent reprocessing the same resume

-  **Resume Parsing**
  - Downloads and extracts text from PDF resumes

-  **AI-Based Evaluation**
  - Compares resume content against a Job Description
  - Produces structured outputs:
    - Score
    - Strengths
    - Weaknesses
    - Overall recommendation

-  **Structured Final Output**
  - Clean candidate records ready for Google Sheets, Docs, or databases

---

## Tech Stack

- **n8n** – Workflow automation
- **OpenAI API** – Resume evaluation and scoring
- **Google Drive** – Resume storage
- **File Hashing** – Deduplication logic
- **JavaScript Expressions** – Data orchestration within n8n

---

##  How It Works

### 1. Resume Intake
- The workflow listens for uploaded resumes.
- A persistent resume reference (Drive link) is captured as metadata.

### 2. Deduplication Layer
- Email deduplication prevents multiple entries for the same candidate.
- Hash-based deduplication ensures the same resume file is not processed again.

### 3. Resume Parsing
- The resume PDF is downloaded.
- Text content is extracted for analysis.

### 4. AI Evaluation
- Resume content + Job Description are sent to OpenAI.
- The model returns structured evaluation data.

### 5. Final Data Assembly
- Candidate personal details and AI evaluation results are **assembled in a final node**, ensuring no data loss.

---

##  What I Learned

### 1. n8n Is Not a Linear Data Pipeline
n8n workflows are **item transformations**, not streams.  
Fields do not persist automatically across nodes.

---

### 2. Metadata Must Be Anchored
Fields like resume references and candidate identifiers:
- Cannot be trusted to flow through binary, AI, or merge nodes
- Must be explicitly fetched from their source nodes

---

### 3. AI Nodes Are Structurally Destructive
AI nodes often:
- Replace the entire JSON object
- Drop unrelated fields silently

The solution was to **assemble data at the end**, not pass it through.

---

### 4. Merge Nodes Can Silently Drop Data
Using `Merge → Choose Branch` forwards only one branch’s data.  
This required a shift from merge-based logic to **explicit data assembly**.

---

##  Challenges Faced & Solutions

###  Issue: Fields Disappearing Mid-Workflow  
**Cause:** Binary + AI nodes overwriting item structure  
**Solution:** Use `$items("SourceNode")` to fetch anchored data

---

###  Issue: Duplicate Resume Processing  
**Cause:** Same file uploaded multiple times  
**Solution:** Hash-based deduplication

---

###  Issue: Only AI Fields Updating in Final Output  
**Cause:** Last Set node overwriting previous data  
**Solution:** Final “assembler” Set node pulling from multiple sources

---

##  Architecture Principle Used

> **Nodes transform data.  
> Set nodes define truth.  
> Final nodes assemble truth.**

This principle made the workflow stable, debuggable, and production-safe.

---

##  Future Improvements

- Persist candidate data in a database (Supabase / PostgreSQL)
- Support multiple job descriptions
- Add evaluation versioning
- Introduce human-in-the-loop review
- Convert into a reusable ATS microservice

---

##  Why This Project Matters

This project demonstrates:
- Real-world automation problem solving
- Deep understanding of n8n internals
- Practical use of AI beyond demos
- Strong debugging and architectural thinking

Built with **production constraints**, not just happy paths (lol)
