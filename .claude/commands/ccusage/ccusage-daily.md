Please run the `ccusage daily` command and then provide a structured markdown summary of the Claude Code usage costs and statistics.

## Required Actions:
1. Execute `ccusage daily` using the Bash tool
2. Parse the output to extract key metrics and statistics
3. Generate a comprehensive markdown report

## Report Format Required:

### Executive Summary
- Total cost for the period
- Date range covered
- Number of usage days
- Average daily cost
- Peak usage day and cost
- Cache efficiency percentage

### Key Statistics Table
A markdown table with:
- Total Tokens
- Input Tokens  
- Output Tokens
- Cache Read Tokens
- Total Cost
- Average Daily Cost

### Daily Cost Summary Table
A compact markdown table showing:
- Date (in MM-DD format)
- Model used
- Input tokens
- Output tokens  
- Cache read tokens
- Total cost for that day

Limit to the top 15-20 highest cost days to keep the table manageable.

### Usage Insights
Provide analysis including:
- Number of high usage days (over $20)
- Cache effectiveness assessment
- Token distribution breakdown
- Primary model identification
- Usage patterns and trends

### Recommendations
Based on the data, provide:
- Cost management insights
- Cache optimization observations
- Usage pattern analysis

Please format everything in clean, readable markdown with proper tables and bullet points. Focus on actionable insights and clear presentation of the cost and usage data.