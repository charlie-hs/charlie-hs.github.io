# CLAUDE.md - AI Assistant Guide

This document provides guidance for AI assistants (especially Claude) working with this personal learning blog.

---

## Project Overview

### Primary Purpose

**Personal Learning Archive**: Record what I learn in TL;DR format so I can easily retrieve and review it later.

### Secondary Purpose

**Daily Learning Tracker**: Track what I need to learn each day. When I encounter topics I don't fully understand, I create detailed blog posts to study them more deeply.

### Core Philosophy

- **Keep it Simple**: Make it easy to maintain daily consistency
- **Quick Capture**: Record TL;DR summaries of daily learnings
- **Deep Dives When Needed**: Create detailed posts only for topics requiring deeper understanding
- **Easy Retrieval**: Organize content for quick searching and reviewing
- **Daily Habit**: Design for sustainability - simple enough to do every day

---

## Project Architecture

### Technology Stack

- **Jekyll**: Static site generator
- **Markdown**: Content format
- **GitHub Pages**: Hosting

### Content Types

1. **TL;DR Posts**: Quick summaries of daily learnings
2. **Deep Dive Posts**: Detailed explanations of topics I want to master
3. **Daily Logs**: Daily learning tracker with checkboxes

### Directory Structure

```
.
├── _posts/                 # All blog posts
│   ├── YYYY-MM-DD-tldr-*.md        # TL;DR summaries
│   ├── YYYY-MM-DD-deep-*.md        # Deep dive posts
│   └── YYYY-MM-DD-daily-log-*.md   # Daily learning logs
├── _config.yml             # Site configuration
├── assets/                 # Images, CSS, JS
└── pages/                  # Static pages (CV, tags, categories)
```

---

## Content Categories

### TL;DR (Quick Learning Summaries)

**Purpose**: Quick daily notes on what I learned

**Characteristics**:
- Short and concise (200-500 words max)
- Bullet points preferred
- Key takeaways only
- Easy to scan and review

**Example Topics**:
- "TL;DR: Learned about Python decorators today"
- "TL;DR: Git rebase vs merge differences"
- "TL;DR: REST API best practices"

**Template**:
```markdown
---
layout: post
title: "TL;DR: [Topic Name]"
date: YYYY-MM-DD
categories: TL;DR
tags: [tag1, tag2]
published: true
---

## What I Learned

- Key point 1
- Key point 2
- Key point 3

## Quick Summary

One-sentence summary of the main takeaway.

## Reference

- [Link to resource if applicable]
```

### Deep-Dive (Detailed Study Posts)

**Purpose**: In-depth exploration of topics I don't fully understand

**Characteristics**:
- Comprehensive explanation
- Code examples
- My own understanding in my own words
- Problem-solving approach

**When to Create**:
- When I encounter something confusing
- When I want to master a concept
- When TL;DR isn't enough

**Template**:
```markdown
---
layout: post
title: "[Topic]: Deep Dive"
date: YYYY-MM-DD
categories: Deep-Dive
tags: [tag1, tag2]
published: true
---

## Why I'm Learning This

Brief explanation of why this topic matters to me.

## The Problem / Question

What confused me or what I want to understand.

## My Understanding

Detailed explanation in my own words.

## Code Examples

Practical examples with comments.

## Key Takeaways

- Main point 1
- Main point 2

## References

- Links to resources I used
```

### Daily-Log (Learning Tracker)

**Purpose**: Track daily learning goals and progress

**Characteristics**:
- Checkbox format
- Daily or weekly posts
- Simple tracking mechanism
- Motivational progress view

**Template**:
```markdown
---
layout: post
title: "Daily Log: [Date/Week]"
date: YYYY-MM-DD
categories: Daily-Log
tags: [daily, learning]
published: true
---

## Today's Learning Goals

- [ ] Topic 1
- [ ] Topic 2
- [ ] Topic 3

## Completed

- [x] Yesterday's topic - [Link to TL;DR or Deep Dive if created]

## Notes

Quick thoughts or reflections.
```

---

## Writing Guidelines

### Post Naming Convention

```
YYYY-MM-DD-category-topic.md
```

**Examples**:
- `2025-10-14-tldr-python-decorators.md`
- `2025-10-14-deep-dive-async-await.md`
- `2025-10-14-daily-log-week-42.md`

### Front Matter (Required)

```markdown
---
layout: post
title: "Your Title Here"
date: YYYY-MM-DD
categories: [TL;DR|Deep-Dive|Daily-Log]
tags: [tag1, tag2, tag3]
published: true
---
```

**Important**:
- `date`: Always use today's actual date (not future dates)
- `categories`: Choose ONE: `TL;DR`, `Deep-Dive`, or `Daily-Log`
- `tags`: Keep it simple, 2-5 tags max
- `published`: Always set to `true` for visible posts

### Content Language

- **Primary**: English (as requested)
- Use clear, simple language
- Technical terms in English
- Code comments in English

---

## AI Assistant Workflow

### When I Say: "Log today's learning"

1. Create a **TL;DR post** with today's date
2. Use bullet points for quick notes
3. Keep it under 500 words
4. Ask me for the main points to include

### When I Say: "I want to deep dive into [topic]"

1. Create a **Deep-Dive post**
2. Structure it with problem → understanding → examples
3. Include code examples if applicable
4. Ask clarifying questions about what confused me

### When I Say: "Create my daily log"

1. Create or update **Daily-Log post**
2. List learning goals for today
3. Link completed items to their TL;DR/Deep-Dive posts
4. Keep the format simple with checkboxes

### General Principles

**Keep It Simple**:
- Don't overcomplicate structure
- Focus on content, not formatting
- Make it easy for daily use

**Be Consistent**:
- Use the same templates
- Follow naming conventions
- Maintain tag consistency

**Support Daily Habit**:
- Quick post creation (< 5 minutes)
- Easy updates
- Minimal friction

---

## Common AI Assistant Tasks

### Creating a TL;DR Post

```
User: "Log what I learned about Docker containers today"

AI Should:
1. Create: 2025-10-14-tldr-docker-containers.md
2. Use TL;DR template
3. Ask: "What are the key points you want to record?"
4. Keep it brief and scannable
```

### Creating a Deep-Dive Post

```
User: "I want to understand async/await better"

AI Should:
1. Create: 2025-10-14-deep-dive-async-await.md
2. Use Deep-Dive template
3. Ask: "What specifically confused you about async/await?"
4. Structure comprehensive explanation
```

### Updating Daily Log

```
User: "Update my daily log"

AI Should:
1. Find today's or this week's daily-log post
2. Mark completed items with [x]
3. Add links to any new TL;DR or Deep-Dive posts created
4. Ask if there are new goals to add
```

---

## Best Practices for AI Assistants

### Do's

✅ Keep posts simple and focused
✅ Use templates consistently
✅ Ask clarifying questions when needed
✅ Link related posts together
✅ Use today's actual date (never future dates)
✅ Prioritize quick capture over perfect formatting
✅ Support daily consistency habit
✅ Keep TL;DR posts brief (under 500 words)

### Don'ts

❌ Don't make posts unnecessarily complex
❌ Don't create new categories without asking
❌ Don't skip the front matter
❌ Don't use future dates
❌ Don't make TL;DR posts too long
❌ Don't add unnecessary formatting
❌ Don't break the simple workflow

---

## Quick Reference

### Three Main Post Types

1. **TL;DR**: Daily quick learning notes (200-500 words)
2. **Deep-Dive**: Detailed study when needed (1000+ words)
3. **Daily-Log**: Learning goals tracker (checkbox format)

### Typical Daily Workflow

```
Morning:
- Create/update daily log with today's goals

During the day:
- Learn things

Evening:
- Create TL;DR posts for what I learned
- Check off completed items in daily log
- Create Deep-Dive post if something needs deeper study
```

### File Naming Pattern

```
2025-10-14-tldr-topic-name.md
2025-10-14-deep-dive-topic-name.md
2025-10-14-daily-log-week-42.md
```

---

## Tips for Using Gemini API

### When Helping Me Create Content

**Ask Short Questions**:
- "What are the 3 main things you learned?"
- "What confused you about this topic?"
- "Should this be a TL;DR or Deep-Dive?"

**Keep Responses Actionable**:
- Provide ready-to-use markdown
- Include proper front matter
- Follow templates exactly

**Support My Habit**:
- Make it quick (< 5 min to create a post)
- Reduce decision fatigue
- Encourage daily consistency

### Content Generation Tips

**For TL;DR**:
- Focus on key takeaways only
- Use bullet points
- Keep it scannable
- Don't explain everything

**For Deep-Dive**:
- Start with the problem/question
- Explain in simple terms
- Include practical examples
- Add code when relevant

**For Daily-Log**:
- Simple checkbox list
- Link to completed posts
- Track progress visibly
- Keep it motivating

---

## Project Goals

This blog aims to be:

1. **Daily Learning Companion**: Support consistent learning habit
2. **Personal Knowledge Base**: Easy to search and review
3. **Progress Tracker**: Visual motivation through daily logs
4. **Low Friction**: Simple enough to maintain every day
5. **Flexible**: TL;DR for quick notes, Deep-Dive when needed

---

## Last Updated

This guide reflects the project structure as of October 2025.

**Current Focus**:
- Keep it simple for daily use
- TL;DR posts for quick learning capture
- Deep-Dive posts for topics requiring more study
- Daily logs for tracking progress

**Remember**: The goal is consistency over perfection. Make it easy for me to maintain daily.

---

## For AI Assistants Using This Guide

**Your Primary Role**: Help me maintain a simple, consistent daily learning habit.

**Key Principles**:
1. Speed over perfection
2. Simple over complex
3. Consistent over creative
4. Supportive over prescriptive

**When in doubt**: Ask me what I prefer, don't guess.