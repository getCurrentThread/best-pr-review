# best-pr-review

GitHub PR을 리뷰하고 리뷰 코멘트를 "좋은 리뷰어"답게 쓰도록 돕는 [Claude Code](https://docs.claude.com/en/docs/claude-code) 스킬입니다.

- 코멘트마다 의도·강도 라벨 (Conventional Comments: `issue (blocking):`, `suggestion:`, `nitpick:` …)
- 사람이 아니라 코드를 주어로, 모든 지적에 "왜"(재현 조건·근거)
- 한두 줄 수정은 GitHub `suggestion` 블록, 구체적인 칭찬 하나 이상
- 결론과 다음 행동이 담긴 요약 코멘트, 코멘트 강도에 맞는 제출 상태 (Approve / Comment / Request changes)
- 반론(pushback) 응답, 코멘트 순화, 영어 오픈소스 PR 표현

기본은 채팅에 초안만 보여주고, 요청할 때만 `gh api`로 리뷰 하나에 묶어 게시합니다.

## 설치

```bash
git clone https://github.com/getCurrentThread/best-pr-review ~/.claude/skills/best-pr-review
```

업데이트는 같은 폴더에서 `git pull`. 게시 기능을 쓰려면 [GitHub CLI](https://cli.github.com/) 로그인(`gh auth login`)이 필요합니다.

## 사용 예

- `PR 123 리뷰해줘`
- `이 리뷰 코멘트 순화해줘: "이거 터져요."`
- `작성자가 "나중에 리팩터링할게요"라는데 어떻게 답하지?`
- `review https://github.com/org/repo/pull/45 and post it`

## 구성

| 파일 | 내용 |
|---|---|
| `SKILL.md` | 리뷰 절차 (순서, 코멘트 형식, 제출 상태, 게시, 반론, 셀프 체크) |
| `references/github-pr-review-guide.md` | 원본 가이드 전문: 라벨 체계 비교, 상황별 Bad → Good 예시, 요약 템플릿, Saved replies |

## 참고 자료

Google Engineering Practices, [Conventional Comments](https://conventionalcomments.org/), GitHub Docs,
뱅크샐러드·화해 코드 리뷰 문화 글, Netlify Feedback Ladders. 링크는 가이드 15장에 있습니다.
