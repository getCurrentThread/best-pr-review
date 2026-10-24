---
name: best-pr-review
description: >-
  Review GitHub pull requests and write review comments the way a good reviewer does: labeled comments
  (Conventional Comments), the code as subject instead of the person, a reason for every point, suggestion blocks,
  specific praise, a summary comment, and a submit state (Approve / Comment / Request changes) that matches comment
  strength. Use for "PR 리뷰해줘", "코드 리뷰 코멘트 써줘", "리뷰 코멘트 다듬어줘", "이 코멘트 순화해줘", "리뷰 요약 써줘",
  "리뷰 반론에 어떻게 답하지", "review this PR", "write review comments", "rewrite my review comment", "respond to
  pushback". Based on Google Engineering Practices, Conventional Comments, GitHub Docs and Korean team practice
  (뱅크샐러드 Pn rule, 화해 L0~L5).
---

# Best PR Review

The full guide, with Bad → Good examples for each situation, is `references/github-pr-review-guide.md` (Korean).
Read the section you need: §5 label systems and mapping table, §7 situation examples, §9 summary templates,
§10 pushback, §11 wording and English OSS phrasing. This file is the working procedure.

## 1. Get the PR

- PR number or URL: `gh pr view <pr> --json title,body,author,baseRefName,headRefName,files,additions,deletions`
  and `gh pr diff <pr>`. Read touched files in full where the diff lacks context.
- Pasted diff or code: review that directly and refer to lines as `L<n>`.
- Only rewriting a comment, replying to pushback, or writing a summary: skip to the matching section.

## 2. Review in this order

1. **PR description: is the change worth making at all?** If not, stop and write one PR-level comment:
   thanks → reason with evidence (ADR, decision, data) → alternative → next step (§7.11). Do not review details yet.
2. **Core files' design.** A big design problem is reported right away, before line nits.
3. **Every remaining file.** Reading the tests first often shows the intent.
4. Too big to review well (roughly over 1,000 lines, or mixed concerns): ask for a split with a concrete order,
   or ask where to start, and give big-picture comments first (§7.12).

Check in priority order: design → functionality (edge cases, concurrency, user impact) → complexity (generality
nobody needs yet) → tests (would they fail if the code broke?) → naming → comments (why, not what) → style (only
what the style guide or linter says) → docs (README, env example, migration notes).

Approval bar: approve when the change clearly improves overall code health, even if imperfect. Do not approve a
change that clearly makes it worse.

## 3. Write each comment

Default format is Conventional Comments:

```markdown
**<label> (<decorations>):** <one-line subject>

<observation: where, under what input or condition, what happens>
<reason: impact, measurement, or link to the convention/doc>

<proposal: alternative, a suggestion block, or a question>
<expectation: in this PR / follow-up issue / optional>
```

- Labels: `issue` (concrete problem, usually blocking; pair it with a fix), `todo` (small required change),
  `chore` (required process step; link the procedure), `suggestion` (always say blocking or non-blocking),
  `question` (unsure; say why you ask), `nitpick` (non-blocking), `thought` (out-of-scope idea), `note`, `praise`,
  plus `typo` and `polish`.
- Decorations: `blocking`, `non-blocking`, `if-minor`, `security`, `performance`, `test`.
- If the team already uses another system (Google `Nit:`/`Optional:`/`FYI:`, 뱅크샐러드 P1–P5, 화해 L0–L5), use
  theirs; the mapping table is §5.5. One system per review.

Rules:

- **The code is the subject, never the person.** "`items` is empty here and L42 throws", not "you missed it".
- **Every blocking comment carries a reproduction (input → result) or evidence** (numbers, doc or convention link).
- **Polite, not vague.** Strength lives in the label, not in hedging. Never wrap a blocker in "혹시… 괜찮으시면…".
- **Rule vs taste.** A rule cites the style guide. Taste says it is taste and is non-blocking (§7.13). Anything a
  formatter or linter could catch: suggest automating it.
- **1–2 line fixes go in a GitHub suggestion block.** It replaces the selected lines entirely, so include unchanged
  lines and keep the original indentation. Big refactors get a direction, not a suggestion block.
- When something was hard to understand, ask for clearer code or a code comment, not an answer that lives only in
  the review thread.
- At least one **specific** praise (what, and why it is good). No fake praise; skip it if nothing earns it.
- Same problem elsewhere: list the other locations (`file:line`).
- Out-of-scope ideas: `thought (non-blocking)` plus an offer to open a follow-up issue.
- Write in the PR's language. English phrasing: §11.2.

## 4. Pick the submit state and write the summary

| State | When |
|---|---|
| Request changes | at least one blocking item (bug, security, data loss, convention violation) |
| Comment | an answer is needed before judging, or only part of the PR was reviewed |
| Approve | no blockers; non-blocking comments may remain ("LGTM with comments") |

The summary comment always states the conclusion and the next action:

- Request changes: blockers with `file:line`, then non-blocking items (optional), then when you will re-review.
- Approve with comments: which items to fix before merge, and that re-review is not needed.
- Comment: the open question and what you will do once it is answered.

Templates: §9.

## 5. Output and posting

Default: a draft in chat, nothing posted. Show the submit state and summary first, then the line comments as
`path:line` + body, blockers first.

Post to GitHub only when the user asks. Post as **one** review so the author gets one notification (the same as
*Start a review*). Write the payload to a temp file outside the repo, then run this from the repo checkout
(`{owner}/{repo}` is filled in by gh; otherwise spell it out):

```bash
gh api repos/{owner}/{repo}/pulls/<pr>/reviews --method POST --input <tmp>/review.json
```

```json
{
  "event": "REQUEST_CHANGES",
  "body": "<summary comment>",
  "comments": [
    {"path": "src/cart.ts", "line": 42, "side": "RIGHT", "body": "**issue (blocking):** ..."},
    {"path": "src/cart.ts", "start_line": 10, "line": 12, "side": "RIGHT", "body": "**nitpick (non-blocking):** ..."}
  ]
}
```

- `event`: `APPROVE`, `COMMENT` or `REQUEST_CHANGES`.
- `line` is the line number in the new file and must fall inside a diff hunk; multi-line comments add `start_line`.
- GitHub refuses Approve and Request changes on your own PR; use `COMMENT` there.

## 6. Pushback

1. First check whether the author is right. If so, withdraw cleanly, and ask to leave the reason as a code comment.
2. If not, show you understood their point and add new evidence.
3. "I'll clean it up later" for complexity this PR introduces: ask for it now, or for an issue created now plus a
   TODO carrying the issue number.
4. Still stuck: suggest a short call, record the conclusion on the PR, escalate to a lead or maintainer if needed.

Examples: §10.

## 7. Self-check before handing over

- [ ] Every comment has a label; blocking ones carry a reproduction or evidence
- [ ] Code as subject; rule and taste kept apart
- [ ] At least one specific praise, if earned
- [ ] 1–2 line fixes given as suggestion blocks
- [ ] Out-of-scope items split into follow-up issues
- [ ] Summary has the conclusion and the next action
- [ ] Submit state matches the strongest comment
