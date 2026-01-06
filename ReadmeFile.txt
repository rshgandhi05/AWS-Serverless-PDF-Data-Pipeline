Assessment Submission- Rishu Gandhi

1. Direction
	1. go to the URL below and download the PDF for the H. Rept. 119-217
report document under Notes -> State-Foreign Operations\(Hse) National Security-Dept of
State section.

URL: https://www.congress.gov/crs-appropriations-status-table/2026
       2. The script must fetch the PDF directly from the site (no preloaded files) and store it locally. Parse the downloaded PDF and retrieve all instances of the phrase “International
Broadcasting Operations” or close variants (e.g., different capitalization, minor word-order
changes).
       3. For each instance found:
	Extract 150 words before and 150 words after the match.
	Capture the page number on which the instance appears.
	Identify and extract any numbers within that surrounding text and store them
separately (as numerical data, not strings).
       4. Save all results in a structured JSON file.
       5. For bonus points, use an LLM of your choice to summarize the context around each
instance of the phrase "International Broadcasting Operations", specifically whether and
how the found numbers affect the budget of that program.


2. Results Below:
   - broadcasting_budget_data.json: The raw structured data
   - broadcasting_budget_with_summary.json: The final enriched report with AI summaries
