# CLAUDE.md - AI Assistant Guide for Charlie's Wiki

This document provides comprehensive guidance for AI assistants working on this personal wiki project.

---

## Project Overview

### Purpose

This is a **Jekyll-based personal wiki** designed for systematic development learning management. It helps developers track their learning journey using a "도장깨기" (achievement stamps) approach - marking off completed learning items like collecting stamps.

### Target Audience

Developers who want to:

- Track their learning progress systematically
- Build a knowledge base of technical topics
- Maintain a portfolio of completed learning items
- Create interconnected learning resources

### Core Philosophy

- **Progressive Learning**: Break down large topics into manageable items
- **Visual Progress**: Use checkboxes to show completion status
- **Interconnected Knowledge**: Link related posts together
- **Documentation as Learning**: Writing about topics reinforces understanding

---

## Project Architecture

### Technology Stack

- **Jekyll**: Static site generator
- **Markdown**: Content format
- **Liquid**: Templating language
- **JavaScript**: Client-side search
- **CSS**: Custom styling with CSS variables

### Key Features

1. **Category System**: Organize posts by major themes (Roadmap, Programming, Computer-Science)
2. **Tag System**: Cross-reference posts by topics
3. **Search**: Client-side search across all content
4. **Roadmap Tracking**: Master progress tracker with checkboxes
5. **Responsive Design**: Mobile-friendly layout
6. **CV Integration**: Professional portfolio page

### Directory Structure

```
.
├── _config.yml              # Site configuration
├── _includes/               # Reusable HTML components
│   ├── header.html         # Navigation + logo + search
│   └── footer.html         # Footer content
├── _layouts/               # Page templates
│   ├── default.html        # Base layout
│   ├── post.html          # Blog post layout
│   ├── tag_page.html      # Tag listing layout
│   ├── category_page.html # Category listing layout
│   └── cv.html            # CV page layout
├── _plugins/               # Custom Jekyll plugins
│   ├── tag_generator.rb   # Auto-generate tag pages
│   └── category_generator.rb  # Auto-generate category pages
├── _posts/                 # All learning posts (Markdown)
│   ├── YYYY-MM-DD-*.md
├── assets/                 # Static files
│   ├── css/style.css      # Main stylesheet
│   ├── js/search.js       # Search functionality
│   └── images/            # Images and logos
├── pages/                  # Fixed pages
│   ├── cv.md              # Curriculum Vitae
│   ├── tags.md            # All tags listing
│   └── categories.md      # All categories listing
└── index.html             # Home page
```

---

## Understanding Sample Posts

The `_posts/` directory contains three sample posts that demonstrate the wiki's structure:

### 1. Roadmap Post: `2025-10-10-backend-roadmap.md`

**Category:** `Roadmap`
**Purpose:** Master learning tracker for backend development

**Structure:**

```markdown
---
layout: post
title: "백엔드 개발자 로드맵: 도장깨기"
date: 2025-10-10
categories: Roadmap
tags: [roadmap, backend, learning]
published: true
---

## 1. 프로그래밍 언어 (Programming Language)

- [x] Python - [[Python 기본 문법 정리](/2025/10/11/python-basics.html)]
- [ ] Java
- [ ] Go

## 2. 자료구조 & 알고리즘

- [x] 스택 (Stack) - [[스택의 이해와 구현](/2025/10/12/data-structure-stack.html)]
- [ ] 큐 (Queue)
- [ ] 트리 (Tree)

## 진행 상황

현재 완료한 항목: **2개**
전체 항목: **약 50개**
진행률: **4%**
```

**Key Features:**

- **Checkbox tracking**: `[x]` = completed, `[ ]` = pending
- **Linked learning**: Each completed item links to its detailed post
- **Progress statistics**: Shows completion percentage
- **Organized by topics**: Groups related skills together

**When to Update:**

- Check off items when you complete learning them
- Add links to newly created detailed posts
- Update progress statistics
- Add new topics as needed

### 2. Programming Post: `2025-10-11-python-basics.md`

**Category:** `Programming`
**Purpose:** Detailed programming language tutorial

**Structure:**

- **Introduction**: What is Python and why learn it
- **Core Concepts**: Variables, data types, control flow, functions
- **Code Examples**: Practical, runnable code snippets
- **Best Practices**: Pythonic patterns
- **Next Steps**: Links to advanced topics

**Content Pattern:**

1. Concept explanation
2. Syntax examples
3. Practical use cases
4. Common patterns
5. "다음 학습" (Next Learning) section

### 3. Computer Science Post: `2025-10-12-data-structure-stack.md`

**Category:** `Computer-Science`
**Purpose:** In-depth technical concept deep-dive

**Structure:**

- **Concept Definition**: What is a Stack?
- **Operations**: Core functionality (push, pop, peek, etc.)
- **Implementation**: Complete Python implementation
- **Time Complexity**: Performance analysis
- **Use Cases**: Real-world applications
- **Practice Problems**: Example implementations

**Educational Value:**

- Theory + Practice combination
- Complete working code
- Multiple examples
- Connections to real-world usage

---

## Content Categories

### Roadmap

**Purpose:** Central progress tracking for learning journeys

**Characteristics:**

- High-level overview of skill areas
- Checkbox-based progress tracking
- Links to detailed learning posts
- Progress statistics
- Long-term tracking document

**Best Practices:**

- One roadmap per major skill area (e.g., Backend, Frontend, DevOps)
- Update regularly as you complete items
- Keep progress statistics current
- Use clear topic groupings

**Example Topics:**

- Backend Developer Roadmap
- Frontend Developer Roadmap
- Data Science Roadmap
- DevOps Engineer Roadmap

### Programming

**Purpose:** Programming language fundamentals and patterns

**Characteristics:**

- Language-specific content
- Syntax and idioms
- Code examples
- Best practices
- Practical exercises

**Typical Content:**

- Language basics (Python, JavaScript, Java, Go)
- Framework tutorials (Django, React, Spring)
- Design patterns
- Code quality practices

### Computer-Science

**Purpose:** Fundamental CS concepts and theory

**Characteristics:**

- Timeless concepts
- Implementation + analysis
- Algorithm complexity
- Mathematical foundations
- Problem-solving techniques

**Typical Content:**

- Data structures (Stack, Queue, Tree, Graph)
- Algorithms (sorting, searching, dynamic programming)
- System design concepts
- Computer architecture
- Operating systems concepts

---

## Writing New Posts

### Post Naming Convention

```
YYYY-MM-DD-topic-name.md
```

**Examples:**

- `2025-10-15-python-decorators.md`
- `2025-10-16-react-hooks-guide.md`
- `2025-10-17-binary-search-tree.md`

### Front Matter Template

```markdown
---
layout: post
title: "Your Post Title"
date: YYYY-MM-DD
categories: [Roadmap|Programming|Computer-Science]
tags: [tag1, tag2, tag3]
published: true
---
```

**Important Notes:**

- `layout`: Always use `post` for content posts
- `title`: Post title (use quotes if it contains special characters)
- `date`: Must be in YYYY-MM-DD format and match the filename date
  - **Always use today's actual date** (not future dates)
  - Jekyll hides future-dated posts by default
  - Example: If today is 2025-10-12, use `date: 2025-10-12`
- `categories`: Use singular form, choose one primary category
- `tags`: Use array format with square brackets
- `published`: Controls post visibility
  - `true` = Post appears on the site (default for all published content)
  - `false` = Draft post, hidden from builds
  - **Always include this field** when creating or updating posts
- Multi-word categories: Use hyphens (e.g., `Computer-Science` not `Computer Space`)

### Content Structure Template

````markdown
---
layout: post
title: "Topic Name: Subtitle"
date: YYYY-MM-DD
categories: Category-Name
tags: [tag1, tag2, tag3]
published: true
---

## Topic Introduction

Brief overview of what this post covers and why it's important.

---

## Main Section 1

### Subsection

Content with examples...

```code
// Code examples
```

---

## Main Section 2

More detailed content...

---

## Practical Examples

### Example 1

Real-world use case...

---

## Summary

Key takeaways from this post.

### Next Learning

- [Related Topic 1]
- [Related Topic 2]
- [Advanced Topic]
````

### Cross-Linking Posts

**Internal Links Format:**

```markdown
[Link Text](/YYYY/MM/DD/post-title.html)
```

**Example:**

```markdown
Learn more about [Python Basics](/2025/10/11/python-basics.html)
```

**When to Link:**

- From roadmap to detailed posts
- Between related topics
- From basic to advanced concepts
- In "Next Learning" sections

---

## AI Assistant Workflow

### When Adding New Learning Posts

1. **Create the post** with proper naming and front matter
   - Include all required fields: `layout`, `title`, `date`, `categories`, `tags`, `published`
   - Set `published: true` for posts ready to be displayed
2. **Update related roadmap** if applicable:
   ```markdown
   - [x] New Topic - [[Link to new post](/YYYY/MM/DD/new-post.html)]
   ```
3. **Update progress statistics** in roadmap
4. **Add cross-references** in related posts
5. **Verify categories and tags** are consistent

### When Updating Roadmaps

1. **Find completed items** that have associated posts
2. **Change checkbox**: `[ ]` → `[x]`
3. **Add link**: `[[Title](/YYYY/MM/DD/post.html)]`
4. **Recalculate progress**:
   ```markdown
   현재 완료한 항목: **X개**
   전체 항목: **Y개**
   진행률: **Z%**
   ```

### Maintaining Consistency

**Categories:**

- Use existing categories when possible
- New categories should be broad themes
- Avoid too many categories (keep it manageable)

**Tags:**

- Use existing tags for consistency
- Create new tags only when needed
- Use CamelCase for multi-word tags (e.g., `DataStructure`)
- Keep tags specific and meaningful

**Formatting:**

- Use Korean for main content (target audience is Korean)
- Use English for code, technical terms, and proper nouns
- Keep code examples well-commented
- Use consistent heading hierarchy

---

## Key Features to Maintain

### 1. Quick Links (바로가기)

**Location:** Home page (index.html)
**Cards:**

- 📚 태그 (Tags)
- 🗂️ 카테고리 (Categories)
- 👤 CV

**Purpose:** Fast navigation to main sections

### 2. Search Functionality

**Location:** Header on all pages
**Features:**

- Real-time client-side search
- Searches title, content, and tags
- Results with highlights
- No server required (static site)

### 3. Logo and Branding

**Logo:** `assets/images/logo/orchwang.png`
**Locations:**

- Header (all pages): 50px height
- CV page: 100px width, centered
- Favicon: Multiple sizes for browsers

**Maintain:** Consistent branding across all pages

### 4. Navigation Menu

**Order:** 홈 | 카테고리 | 태그 | CV
**Keep:** Same order in header and quick links

### 5. Responsive Design

- Mobile-friendly layouts
- Logo scales down on mobile
- Navigation adapts for small screens
- Touch-friendly buttons and links

---

## Best Practices

### For Roadmap Management

1. **Start broad, get specific**: Main topics → subtopics → detailed items
2. **Link everything**: Every checked item should link to a learning post
3. **Update regularly**: Review and check off items as you learn
4. **Track progress**: Keep statistics current for motivation
5. **One roadmap per domain**: Don't mix unrelated topics

### For Technical Posts

1. **Start with why**: Explain importance before diving into details
2. **Show, don't just tell**: Include working code examples
3. **Multiple examples**: Show different use cases
4. **Explain complexity**: Include time/space complexity for algorithms
5. **Link to next steps**: Guide readers to related topics

### For Knowledge Management

1. **Write as you learn**: Don't wait until you're an expert
2. **Link related content**: Build a web of knowledge
3. **Update old posts**: Improve clarity based on new understanding
4. **Use consistent formatting**: Makes content easier to navigate
5. **Tag thoughtfully**: Makes content discoverable

---

## Common Tasks

### Adding a New Learning Post

```bash
# Use the custom command
/add-post "Python Decorators" Programming "Python,Programming,Advanced"
```

This creates:

- Properly named file in `_posts/`
- Correct front matter
- Basic content template

### Checking Site Locally

```bash
# Start Jekyll server
make serve

# Access at http://localhost:4000
```

### Finding Content

- **By category:** Visit `/pages/categories.html`
- **By tag:** Visit `/pages/tags.html`
- **By search:** Use search box in header

---

## Current State Summary

### Existing Content

- **3 sample posts** demonstrating the wiki structure
- **1 roadmap** (Backend Developer) with 50 items, 2 completed
- **2 detailed posts** (Python Basics, Stack Data Structure)

### Categories

- **Roadmap**: Learning journey trackers
- **Programming**: Language and framework tutorials
- **Computer-Science**: CS fundamentals

### Tags in Use

- `roadmap`, `backend`, `learning`
- `Python`, `Programming`, `Basics`
- `DataStructure`, `Algorithm`, `CS`, `Stack`

### Site Features

- ✅ Category pages with auto-generation
- ✅ Tag pages with auto-generation
- ✅ Client-side search
- ✅ CV/portfolio page
- ✅ Logo and favicon
- ✅ Responsive design
- ✅ Quick links on home page

---

## Guidelines for AI Assistants

### Do's

- ✅ Update roadmap checkboxes when completing topics
- ✅ Cross-link related posts
- ✅ Keep progress statistics current
- ✅ Use consistent categories and tags
- ✅ Always include `published: true` in post front matter
- ✅ Write clear, educational content
- ✅ Include practical code examples
- ✅ Add "Next Learning" sections
- ✅ Maintain responsive design
- ✅ Follow established naming conventions

### Don'ts

- ❌ Create new categories unnecessarily
- ❌ Skip front matter in posts
- ❌ Break existing links when renaming
- ❌ Mix multiple categories per post
- ❌ Use spaces in category names (use hyphens)
- ❌ Ignore cross-referencing opportunities
- ❌ Leave roadmaps outdated
- ❌ Create posts without linking to roadmap

---

## Project Goals

This wiki aims to be:

1. **Comprehensive**: Cover all essential topics for target skill area
2. **Interconnected**: All posts link to related content
3. **Progressive**: Clear learning path from basics to advanced
4. **Maintainable**: Easy to update and extend
5. **Motivating**: Visual progress tracking encourages continued learning

---

## Last Updated

This guide reflects the project structure as of October 2025.

**Current Status:**

- Jekyll wiki fully implemented
- Category system operational
- Tag system operational
- 3 sample posts demonstrating structure
- Logo and branding applied
- Search functionality working
- CV page integrated

**Next Steps:**

- Continue adding learning posts
- Update roadmap progress
- Build knowledge connections
- Maintain consistent quality

---

For questions or clarifications about this project, refer to:

- `specs/blueprint.spec.md` - Original design specification
- `README.md` - Setup and usage instructions
- Sample posts in `_posts/` - Content examples
