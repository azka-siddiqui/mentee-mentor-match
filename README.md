# [STEMing UP](https://hervolution.org/blog) F25 Mentorship Matching

## Overview

This project implements an **automated, data-driven mentor–mentee matching system** designed to pair mentees with mentors in an efficient, scalable, and cohesive way.

Using survey responses from both mentors and mentees, the system combines structured data, natural language processing, and optimization algorithms to generate high-quality matches while respecting mentor capacity constraints. Each match is accompanied by a human-readable justification explaining why the pairing works well.

## Why This Exists

Manual mentor–mentee matching:
- Can unintentionally introduce bias
- Makes it difficult to balance mentor capacity
- Lacks transparency for participants

This system was built to:
- Scale matching objectively
- Respect mentor availability and preferences
- Incorporate both quantitative and qualitative signals
- Reduce administrative overhead 

---

## How It Works

At a high level, the system follows five stages:

1. **Data Ingestion**  
   Mentor and mentee survey responses are loaded from CSV files.

2. **Feature Extraction**  
   - Multiple-choice responses are encoded as structured numerical features.
   - Free-text responses are converted into semantic vectors using TF–IDF.

3. **Compatibility Scoring**  
   Each mentee is compared against every mentor to measure alignment across interests, goals, communication style, and availability.

4. **Optimal Assignment**  
   A global optimization algorithm assigns mentees to mentors while respecting mentor capacity limits.

5. **Explainability & Output**  
   Natural-language justifications are generated for each match, results are saved to CSV, and a network visualization is created.

---

## Technical Details

### Data Sources

- **Mentees CSV**: `f25_mentees.csv`
- **Mentors CSV**: `f25_mentors.csv`

Each file includes:
- Identification fields (name, email)
- Structured survey responses (e.g., timezone, interests, meeting frequency)
- Open-ended text responses (e.g., goals, hobbies, motivation)

---

### Features

#### Structured (Multiple-Choice) Data
- Mentor and mentee feature spaces are aligned for comparability
- Examples:
  - Timezone
  - Fields of interest
  - Social style
  - Desired meeting frequency
  - Skills sought or offered

#### Free-Text Data
- Relevant text fields are concatenated per participant
- Vectorized using **TF–IDF**
- Captures semantic similarity between mentor and mentee responses

---

### Mentor Capacity Handling

Mentors may take multiple mentees.

To support this:
- Each mentor is expanded into multiple **mentor slots** based on declared capacity
- Each slot represents a single possible assignment
- This converts the problem into a one-to-one matching formulation

---

### Matching Algorithm

- **Similarity metric**: Cosine similarity
- **Optimization method**: Hungarian Algorithm (`linear_sum_assignment`)

This guarantees:
- Each mentee is matched to at most one mentor
- Mentor capacity constraints are respected
- Overall compatibility across the cohort is maximized (global optimum)

---

### Explainability with OpenAI

For each matched pair:
- A prompt is generated using mentor and mentee survey data
- A concise, natural-language explanation is produced using the OpenAI API
- Ensures the matching process is transparent and easy to communicate.

---

## Outputs

### 1. Match Results CSV
`f25_mentee_mentor_matches.csv` contains:
- Mentor and mentee identifiers
- Compatibility scores
- Natural-language justifications
- Academic and demographic metadata
- Unmatched mentees (if applicable)
- 
---

## Requirements

- Python 3.11+
- pandas
- numpy
- scikit-learn
- scipy
- networkx
- matplotlib
- python-dotenv
- OpenAI Python SDK

Create a `.env` file with:
```env
OPENAI_API_KEY=your_api_key_here
