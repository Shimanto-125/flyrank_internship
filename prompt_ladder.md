# Improving an AI Prompt for SEO Recommendations

**Course:** FlyRank ML Internship – Prompt Engineering Assignment  
**Track:** Machine Learning  
**Author:** *Md. Abir Shimanto*  
**Date:** July 2026

---

# Objective

The purpose of this exercise is to improve a weak AI prompt by changing **exactly one prompt engineering layer at a time**. Each version is compared with the previous one to understand which modification improves the AI's response and why.

---

# Baseline (Run 1)

## Prompt

> Help me improve my website SEO.

---

## Representative Output

- Improve your page titles.
- Add more keywords.
- Improve page speed.
- Build backlinks.
- Write high-quality content.
- Use meta descriptions.

---

## Notes

### What changed in the prompt
No changes. This is the original weak prompt.

### What improved in the output
Nothing. The output is generic and could apply to almost any website.

### What still failed
The AI has no idea what type of website it is helping, so the recommendations are vague.

### What I would try next
Provide a clear goal for the AI.

---

# Version 1 – Add a Clear Goal

## Layer Added

**Clear Goal**

---

## Prompt

> Help me improve my website SEO so it ranks higher on Google for AI internship and machine learning portfolio searches.

---

## Representative Output

- Target keywords related to AI internships.
- Improve page titles with internship-related phrases.
- Publish project pages optimized for machine learning searches.
- Improve internal linking between projects.

---

## Comparison with Previous Output

| Baseline | Version 1 |
|-----------|-----------|
| Generic SEO advice | SEO advice focused on internship-related searches |

---

## Notes

### What changed in the prompt
Added a specific ranking goal.

### What improved in the output
The AI focused on AI internship and machine learning keywords instead of giving generic SEO tips.

### What still failed
The AI still doesn't know what kind of website it is optimizing.

### What I would try next
Provide real context about the website.

---

# Version 2 – Add Real Context

## Layer Added

**Context**

---

## Prompt

> Help me improve my website SEO so it ranks higher on Google for AI internship and machine learning portfolio searches.
>
> The website is my personal portfolio containing machine learning projects, competitive programming achievements, research work, and technical blogs.

---

## Representative Output

- Create dedicated project pages.
- Add GitHub repository links.
- Publish research summaries.
- Improve internal linking between blogs and projects.
- Create an "About Me" page optimized for recruiter searches.

---

## Comparison with Previous Output

| Version 1 | Version 2 |
|-----------|-----------|
| General internship advice | Portfolio-specific recommendations |

---

## Notes

### What changed in the prompt
Added website context.

### What improved in the output
Recommendations became relevant to a technical portfolio instead of a general website.

### What still failed
The response is still presented as a long paragraph and is difficult to prioritize.

### What I would try next
Specify an output format.

---

# Version 3 – Specify Output Format

## Layer Added

**Output Format**

---

## Prompt

> Help me improve my website SEO so it ranks higher on Google for AI internship and machine learning portfolio searches.
>
> The website is my personal portfolio containing machine learning projects, competitive programming achievements, research work, and technical blogs.
>
> Present the recommendations in a table with three columns:
>
> - SEO Issue
> - Recommendation
> - Expected Impact

---

## Representative Output

| SEO Issue | Recommendation | Expected Impact |
|------------|---------------|----------------|
| Weak Titles | Add keyword-rich titles | High |
| Missing Internal Links | Link related projects | Medium |
| Thin Project Descriptions | Expand technical details | High |
| Missing Schema | Add structured data | Medium |

---

## Comparison with Previous Output

| Version 2 | Version 3 |
|-----------|-----------|
| Long paragraphs | Organized table |

---

## Notes

### What changed in the prompt
Specified a table format.

### What improved in the output
The response became easier to read and compare.

### What still failed
**This change did not significantly improve the quality of the SEO recommendations. It only improved presentation.**

### What I would try next
Add practical constraints.

> **Honest Reflection:** This version mostly improved formatting rather than the actual quality of the advice.

---

# Version 4 – Add Constraints

## Layer Added

**Constraints**

---

## Prompt

> Help me improve my website SEO so it ranks higher on Google for AI internship and machine learning portfolio searches.
>
> The website is my personal portfolio containing machine learning projects, competitive programming achievements, research work, and technical blogs.
>
> Present the recommendations in a table.
>
> Recommend only actions that one person can complete within one week without using paid SEO tools.

---

## Representative Output

| Recommendation | Time |
|---------------|------|
| Rewrite page titles | 1 hour |
| Improve headings | 2 hours |
| Add internal links | 3 hours |
| Create meta descriptions | 2 hours |
| Optimize images | 2 hours |

---

## Comparison with Previous Output

| Version 3 | Version 4 |
|-----------|-----------|
| Included expensive possibilities | Only realistic, low-cost tasks |

---

## Notes

### What changed in the prompt
Added implementation constraints.

### What improved in the output
The recommendations became practical and achievable for an individual developer.

### What still failed
The AI still does not prioritize which recommendations matter most.

### What I would try next
Require prioritization and quality criteria.

---

# Version 5 – Add Quality Criteria

## Layer Added

**Quality Criteria**

---

## Prompt

> Help me improve my website SEO so it ranks higher on Google for AI internship and machine learning portfolio searches.
>
> The website is my personal portfolio containing machine learning projects, competitive programming achievements, research work, and technical blogs.
>
> Present the recommendations in a table.
>
> Recommend only actions that one person can complete within one week without paid SEO tools.
>
> Rank every recommendation by expected SEO impact and explain why it deserves its priority.

---

## Representative Output

| Priority | Recommendation | Why |
|-----------|---------------|-----|
| High | Improve page titles | Titles strongly influence search relevance. |
| High | Expand project pages | Rich content increases ranking opportunities. |
| Medium | Add structured data | Helps search engines understand content. |
| Medium | Improve internal linking | Distributes page authority. |
| Low | Compress images | Improves loading speed but has smaller impact. |

---

## Comparison with Previous Output

| Version 4 | Version 5 |
|-----------|-----------|
| Flat recommendation list | Prioritized action plan |

---

## Notes

### What changed in the prompt
Added quality criteria requiring prioritization.

### What improved in the output
The recommendations became easier to execute because they were ranked by expected SEO impact with clear explanations.

### What still failed
The AI assumes keyword opportunities instead of verifying them with search data.

### What I would try next
Require keyword validation using real search volume data before making recommendations.

---

# Final Reusable Prompt

> You are an experienced SEO consultant specializing in personal technical portfolios.
>
> **Goal**
>
> Improve my portfolio so it ranks higher on Google for AI internship, machine learning, software engineering, and computer science research related searches.
>
> **Context**
>
> My website contains machine learning projects, competitive programming achievements, technical blogs, research papers, GitHub repositories, and personal information for recruiters.
>
> **Output Format**
>
> Return a table containing:
>
> - Current SEO Issue
> - Recommendation
> - Reason
> - Expected SEO Impact
> - Implementation Difficulty
> - Priority
>
> **Constraints**
>
> Recommend only actions that one developer can complete within one week without using paid SEO tools.
>
> **Quality Criteria**
>
> - Prioritize recommendations by expected ranking impact.
> - Explain each recommendation briefly.
> - Avoid generic advice.
> - Focus on practical improvements suitable for a technical portfolio.
> - Clearly identify the highest-impact actions first.

---

# Reflection

This exercise demonstrated that prompt engineering works best when only one change is introduced at a time. The baseline prompt produced generic recommendations because it lacked direction and context. Adding a clear goal significantly improved relevance, while providing context made the advice specific to a machine learning portfolio. Specifying an output format improved readability but did not meaningfully improve the quality of the recommendations, showing that better formatting does not always produce better reasoning. Practical constraints made the suggestions more realistic, and quality criteria transformed the output into a prioritized action plan. Overall, changing one prompt element at a time made it easy to understand which improvements genuinely increased the usefulness of the AI's responses.

---

# Conclusion

The final prompt is reusable by anyone building a technical portfolio for AI or machine learning careers. It provides sufficient context, clear objectives, practical constraints, and quality expectations, resulting in responses that are focused, actionable, and easier to implement than those generated from the original weak prompt.