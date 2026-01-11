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

## How the Matching Script Works

This notebook takes two input files (1 listing mentees, 1 listing mentors) and produces a CSV of optimal mentor-mentee pairings under each mentor’s capacity limits.

1. **Feature extraction**  
   - **Multiple-choice questions** (program, skills, timezone, meeting frequency, etc.) are converted into one-hot vectors.  
   - **Free-text answers** are combined and converted into TF-IDF vectors.  
   - Both sets of features are stacked into a single sparse matrix for mentees (`X`) and for mentors (`Y`).

2. **Capacity expansion**  
   - Each mentor can take on 1 or more mentees (as specified in their “capacity” column).  
   - We “expand” each mentor into that many _slots_ so that matching sees every available spot.

3. **Global optimization**  
   - We compute cosine similarity between every mentee and every mentor-slot, resulting in a single similarity matrix.  
   - We then build a “cost” matrix (`1 – similarity`) and feed it into the Hungarian algorithm.  
   - This finds the assignment of mentees to slots that **maximizes total compatibility** across _all_ matches, while ensuring no mentor is over-assigned.

4. **Resulting CSV**  
   - The script writes `s25_mentee_mentor_matches.csv`, which lists for each matched pair:  
     - The mentee’s email  
     - The mentor’s email  
     - A numeric compatibility score (0.0–1.0)  
     - A one-sentence, human-readable justification generated via the OpenAI API  

Because the Hungarian solver considers every possible pairing at once, it guarantees the best overall outcome: you may see some individual mentee scores dip (versus a purely greedy, one-by-one approach), but the _total_ compatibility sum is as large as possible under the exact capacity constraints. That way, even if there aren’t enough slots for every mentee, you know you’re getting the strongest pool of matches overall.

![Mentor–Mentee Match Graph](https://github.com/ammiellewb/techplus-s25-mentorship/blob/main/Mentor%E2%80%93Mentee%20Match%20Graph.png](https://github.com/azka-siddiqui/mentee-mentor-match/blob/main/Mentor%E2%80%93Mentee%20Match%20Graph.png "Mentor–Mentee Match Graph")

---

**Tip:** If you ever need to tweak what “compatibility” means (eg. by weighting certain questions more heavily), you can adjust the feature stacking step before the similarity calculation and rerun the notebook.

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
