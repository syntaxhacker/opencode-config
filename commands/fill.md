---
description: Auto-fill a job application form with Rohit's profile using Chrome DevTools. Usage: /fill <url>
---

You have the Chrome DevTools tool available. Use it to fill the job application form at this URL: $1

## Instructions

1. Navigate to $1 using `new_page` or `navigate_page`
2. Take a snapshot of the page to find form fields
3. Click any "Apply Now", "Submit profile", or application buttons to reveal the form
4. Identify all form fields (text inputs, textareas, selects, checkboxes, file uploads)
5. Fill every field using `fill_form`, `fill`, or `click` where appropriate

## Profile Data to Use

Use this data to fill the form:

**Personal:** Rohit Jogi | jrohit072@gmail.com | +91 8179948668 | Visakhapatnam, Andhra Pradesh, India

**Links:** Portfolio: https://me2025.vercel.app/ | GitHub: https://github.com/syntaxhacker | LinkedIn: https://www.linkedin.com/in/rohitjogi/

**Summary:** Technical Lead & Full Stack Developer with 5+ years of experience building logistics SaaS platforms, leading teams of 10+ developers, and shipping AI-powered solutions using Claude Code, Codex CLI, OpenCode CLI, and Roo Code.

**Skills:** React & Redux, Node.js & Python, Technical Leadership, System Architecture, AI Agents & Agent Harness, LLMs & RAG Pipelines, MySQL & Redis, Vite & RTK Query, Supply Chain SaaS, Claude Code, Codex CLI, OpenCode CLI, Roo Code

**Education:** B.Tech (CSE, Lateral Entry) — ANITS, Visakhapatnam (2017–2018)

**Experience:**
- Technical Lead at Imaginnovate (FleetEnable Product) — Feb 2024 to May 2026. Led UI development, built AI chatbot & RAG pipeline, leveraged AI agents, mentored 10+ developers, conducted 100+ interviews.
- SDE 3 at Imaginnovate (FleetEnable Product) — Dec 2021 to Oct 2024. Built Python dashboards for logistics, mentored developers, conducted interviews.
- Software Engineer at Coditude — Jan 2021 to Dec 2021. Built Chrome Extensions, LVRG supply chain platform, Twilio/Stripe integrations.
- Freelance Frontend Developer — 2018 to 2020. HTML, CSS, JavaScript, React projects.

**Cover Letter:**
I'm excited to apply to your team. As a Technical Lead & Full Stack Developer with 5+ years of experience building production logistics SaaS platforms, I've led frontend architecture using React, Redux, Vite, and RTK Query at FleetEnable — a growth-stage startup. I've mentored 10+ developers, built AI chatbots with RAG pipelines, and deeply leverage AI agents (Claude Code, Codex CLI, OpenCode CLI) to accelerate development. I'm drawn to teams solving hard problems with autonomy and would love to contribute.

## Resume Upload

The portfolio PDF is at `/home/mysyntax/Documents/me_2025/pdf/rohit_jogi_resume.pdf`. If the form has a file upload field, upload this file using `upload_file`.

## Critical Rules

- Fill ALL fields you can find — name, email, phone, links, cover letter, education, employment history, skills
- For dropdown/select fields (like department), choose the most relevant option (e.g. "Front End Developer", "Engineering", "Technology")
- Check consent/privacy checkboxes
- **DO NOT click Submit Application or Submit** — leave the form filled and ready for manual review
- If you need to add multiple employers or education entries, click the "+ Add" buttons
- Skip date picker fields only if they're too complex (readonly/datepicker inputs)
