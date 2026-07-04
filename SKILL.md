---
name: lectures-maker
description: Create, revise, version, and publish university-style lecture handouts and teaching notes. Use when the user asks to make lecture notes, student handouts, course materials, class worksheets, Markdown lecture drafts, revise lecture content from uploaded images/PDF/Word/Markdown, manage lecture versions, or export final lecture materials to DOCX. Enforces Markdown-first authoring, single-source Markdown editing, and Git-based version management for every lecture project.
---

# Lectures Maker

## Core Rule

Always create or update the Markdown lecture first. Do not create DOCX/PDF as the first artifact. Use one active Markdown file as the source of truth, then export only when the user confirms the lecture is complete.

Use the lecture title as the project identity. Derive a safe file prefix from the title, for example:

```text
第1讲：AI时代，我们为什么要学高中数学
-> 第1讲-AI时代我们为什么要学高中数学
```

For new projects, name the Markdown source after the lecture title:

```text
<lecture-title>.md
```

Do not create a new Markdown file for every revision. Since Git records every change, edit the existing Markdown source file directly and commit each meaningful change.

## Required Project Structure

For each lecture project, keep these files in the lecture workspace:

```text
<lecture-title>.md             # single Markdown source of truth
change_log.md                  # generated and updated after every change
<lecture-title>_final.docx     # final Word output after confirmation
```

If an existing project already has multiple versioned Markdown files, identify the latest Markdown file, make it the active source file, and continue editing that file unless the user asks to consolidate or rename.

## Mandatory Git Version Management

All lecture projects must use Git.

Before editing, check repository state:

```bash
git status --short
```

If the workspace is not a Git repository, initialize one before creating or modifying lecture files:

```bash
git init
```

After every generated draft, revision, file ingestion, directed edit, or final export:

1. Update or create `change_log.md`.
2. Run `git add` for changed lecture files.
3. Run `git commit`.
4. Generate the commit message from the actual change.
5. Preserve full history; never rewrite history unless the user explicitly asks.
6. Support rollback by telling the user which commit/tag to return to.
7. Support branches for different teaching versions.
8. On final publication, create a Git tag.

Use commit messages like:

```text
Create AI-era math bridge lecture draft
Revise lecture into student handout format
Add set notation exercises from uploaded image
Export final DOCX handout
```

Final tag format:

```text
<safe-lecture-title>-final-v1
```

If the tag already exists, increment the suffix.

When the user requests a branch for a variant, use a clear branch name:

```text
teaching-version/<safe-lecture-title>-advanced
teaching-version/<safe-lecture-title>-student-handout
```

Never revert user changes without explicit permission. If the worktree contains unrelated changes, commit only the files relevant to the lecture task.

## Phase 1: Generate The First Markdown Draft

When the user provides course name, lesson topic, audience, duration, and requirements, gather or infer:

```text
reference materials
knowledge-point outline
important formulas
case materials
common examples
engineering or life scenarios
error-prone points
exam focus
```

Then create the Markdown source file with this structure unless the user specifies another one:

```markdown
# 第X讲：xxxx

## 一、学习目标

## 二、知识框架

## 三、知识点1

### 定义

### 原理

### 公式

### 示例

## 四、知识点2

## 五、课堂练习

## 六、总结
```

For student handouts, keep the language concise. Prefer clear definitions, comparison tables, prompts, examples, fill-in blanks, and exercises. Avoid teacher-only commentary unless the user asks for a teacher version.

After writing the draft, update `change_log.md`, then `git add` and `git commit`. Future revisions must edit this same Markdown file in place.

## Phase 2: Analyze Uploaded Materials And Insert Semantically

When the user uploads images, PDFs, Word files, Markdown files, or exercise screenshots:

1. Extract titles, text, formulas, charts, questions, answers, and knowledge points.
2. Build:

```text
新增知识点列表
新增习题列表
新增案例列表
```

3. Insert content by meaning, not at the end:

```text
concept question -> after the related concept
calculation question -> after the related formula
comprehensive problem -> case analysis or homework
real exam question -> classroom exercise or homework
image/table -> near the related explanation
```

Keep logic complete, heading levels correct, and numbering continuous. State the insertion position in `change_log.md`.

Edit the active Markdown source file in place, then commit.

## Phase 3: Directed Edits

When the user asks to delete, modify, expand, simplify, adjust difficulty, add cases, add exercises, or change the audience:

1. Read the active Markdown source file.
2. Modify only the requested sections.
3. Keep all unspecified content unchanged.
4. Update headings, numbering, and cross references only as needed.
5. Save the same Markdown source file in place.
6. Update `change_log.md` with the modified sections and reason.
7. Commit the change.

If the user says “其余没有提到的部分禁止修改” or similar, edit only the requested text ranges in the active Markdown file. Do not copy to a new version file; use Git history for rollback.

## Phase 4: Final Export

Only export DOCX when the user says the lecture is complete, such as:

```text
讲义确认完成
没有问题了
基本没有什么问题了，请转换成docx
```

Before export, check:

```text
title format
heading hierarchy
image references
formula/code block format
numbering and tables
change_log.md completeness
clean Markdown structure without meaningless blank lines
```

DOCX requirements:

```text
Heading 1 for first-level headings
Heading 2 for second-level headings
automatic table of contents field when appropriate
centered images when present
polished but plain tables
page numbers
header with course or lecture name when appropriate
Chinese text in SimSun/宋体
English text in Times New Roman
mathematical formulas entered as Word equation objects, not screenshots or decorative images
white page background only
black body text
no decorative background colors or colored fills
no meaningless blank lines
no dedicated note areas unless the user explicitly requests them
```

Use the documents skill when generating DOCX. Convert the Markdown directly and preserve the lecture content rather than adding extra note boxes, background colors, or decorative layout. Render and visually inspect if the environment has a DOCX renderer. If rendering is unavailable, perform structural checks and tell the user render QA could not be completed.

After final DOCX creation:

1. Update `change_log.md`.
2. Commit the DOCX and log.
3. Create a final tag.
4. Report the final DOCX path and the tag name.

## Change Log Format

Append one entry per change or export:

```markdown
## YYYY-MM-DD

- 版本：`<active-md-file-or-final-docx>`
- 修改章节：<sections changed>
- 修改原因：<why changed>
- 插入位置说明：<where new content was inserted, or state that this is final export>
- Git提交：`<short-commit-hash>`
```

If the commit hash is not known until after commit, append the hash in a quick follow-up edit and commit that small log update, or include the previous commit hash when appropriate. Prefer a complete log over a perfectly single-commit log entry.

## Response Style

Keep user-facing responses short and operational:

```text
已更新 <file>。
已提交 Git commit: <hash> <message>。
本次新增内容插入在：<location>。
```

For final export, cite only the final DOCX unless the user asks for intermediate files.
