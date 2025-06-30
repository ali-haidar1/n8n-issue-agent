🤖 AI-Powered GitHub Issue Triage – n8n Workflow

📝 Overview

This project is an n8n workflow that automatically processes new GitHub issues using AI.Each issue is categorized, summarized, sentiment-analyzed, tagged, assigned to the correct team, and stored in a Supabase database.Bonus features include dynamic tag extraction and (extendable) smart notifications.

![image](https://github.com/user-attachments/assets/d3cfccb8-5a12-40cb-b27d-fa12eb7d836c)

⚙️ Workflow Logic

🔄 Deduplication (“Since Last Run”) Logic

To ensure the workflow processes only new or updated GitHub issues and avoids duplicates, I implemented a persistent timestamp (“since last run”) mechanism using n8n’s static data feature:

Retrieve the last successful run’s timestamp from n8n’s static data storage.

If this is the first run, default to an old date.

Use this timestamp as the since parameter in the GitHub Issues API request.

This ensures only issues created or updated after the previous run are fetched and processed.

🧠 Enrichment Steps

🏷️ Categorization:AI classifies the issue as Bug, Feature Request, Question, or Other based on title/body content.

📝 Summary:AI generates a one-sentence summary of the issue body.

👥 Role Assignment:AI assigns issues to Engineering, Sales, Support, or Other, based on content.

😀 Sentiment Analysis:AI labels the issue as Positive, Negative, or Neutral.

🏷️ Tag Extraction:AI extracts up to 5 relevant, lower-case, one-word tags for each issue.

🗄️ Supabase Storage

Stores enriched issue data in the enriched_issues table with deduplication on issue_url.

Database link: [Supabase Project](https://supabase.com/dashboard/project/cvtkomxkqqyleneooejy/database/schemas)


![image](https://github.com/user-attachments/assets/0f205608-9f42-4826-81db-19d8196d3e6b)


![image](https://github.com/user-attachments/assets/c9bc8376-b24e-497e-b3ea-b64179deb1b8)

![image](https://github.com/user-attachments/assets/0ea1670b-5f7e-4c4b-93a1-568cdcd970fc)

![image](https://github.com/user-attachments/assets/9f14aa04-2101-4329-af69-77c5668cc84d)



🤖 AI Prompt Used

```You are an AI assistant tasked with processing new GitHub issues. For each issue, follow these steps and return the result as a JSON object with these fields:

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
```

🛑 Known Limitations

⚠️ Team Notifications Not Implemented:The workflow does not currently send Slack or Discord notifications, as this feature was deprioritized due to time constraints. The Switch node logic can be easily extended in the future.

⚠️ Workflow may require tuning if repo structure or business logic differs.
