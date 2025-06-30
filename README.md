# AI-Powered GitHub Issue Triage – n8n Workflow

## Overview

This project is an n8n workflow that automatically processes new GitHub issues using AI. Each issue is categorized, summarized, sentiment-analyzed, tagged, assigned to the correct team, and stored in a Supabase database. Bonus features include smart team notifications and dynamic tag extraction.

---

## Workflow Logic

- **Deduplication:**  
  Only processes new issues since the last successful run using the GitHub `since` parameter, managed via n8n static data.

- **Enrichment Steps:**
  1. **Categorization:**  
     AI classifies the issue as "Bug", "Feature Request", "Question", or "Other" based on title/body content.
  2. **Summary:**  
     AI generates a one-sentence summary of the issue body.
  3. **Role Assignment:**  
     AI assigns issues to "Engineering", "Sales", "Support", or "Other", based on content.
  4. **Sentiment Analysis:**  
     AI labels the issue as "Positive", "Negative", or "Neutral".
  5. **Tag Extraction:**  
     AI extracts up to 5 relevant, lower-case, one-word tags for each issue.

- **Supabase Storage:**  
  Stores enriched issue data in the `enriched_issues` table with deduplication on `issue_url`.  

- **Smart Notifications:**  
  Uses a Switch node to send issues to different Slack or Discord channels based on the assigned team.

---

## AI Prompt Used

```text
You are an AI assistant tasked with processing new GitHub issues. For each issue, follow these steps and return the result as a JSON object with these fields:

1. Categorize the Issue (category): Choose "Bug", "Feature Request", "Question", or "Other".
2. Generate a Summary (summary): Write a clear, one-sentence summary of the issue body.
3. Assign to a Role (role): Assign to "Engineering", "Sales", "Support", or "Other".
4. Sentiment Analysis (sentiment): Classify as "Positive", "Negative", or "Neutral".
5. Tag Extraction (tags): Extract up to 5 relevant, lower-case, one-word tags. Return as a JSON array.

ISSUE DETAILS:
- title: {{ $json.title }}
- body: {{ $json.body }}
- creator_login: {{ $json.creator_login }}
- html_url: {{ $json.html_url }}

RETURN FORMAT (JSON):
{
  "title": "i need to",
  "creator_login": "...",
  "issue_url": "..",
  "category": "...",
  "summary": "...",
  "assigned_role": "...",
  "sentiment": "...",
  "tags": ["...", "...", "..."]
}

Known Limitations

Team Notifications Not Implemented:
The workflow does not currently send Slack or Discord notifications, as this feature was deprioritized due to time constraints. The Switch node logic can be easily extended in the future.
