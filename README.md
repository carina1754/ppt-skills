# ppt-skills

발표 자료를 만들 때 쓰는 Claude Code 스킬 묶음입니다. 저장소 하나가 그대로 플러그인 마켓플레이스라서, 팀원은 명령 두 줄로 설치하고 `/plugin marketplace update` 한 번으로 새 판을 받습니다.

## 들어 있는 것

| 플러그인 | 하는 일 |
|---|---|
| `ppt-maker` | 팀 공용 절차. 청중과 목적 정리, 주장 제목 개요를 먼저 확인받고, 형식을 고르고(HTML 슬라이드 또는 .pptx), 한국어 글꼴과 표기, AI 티 점검표, 모든 장을 그림으로 떠서 확인 |
| `frontend-slides` | HTML 슬라이드 생성기. 스타일 미리보기 3개 중 고르기, 템플릿 모음, 기존 .pptx를 웹 슬라이드로 변환, PDF 내보내기 |

고칠 수 있는 .pptx가 필요하면 Anthropic 공식 `pptx` 스킬도 같이 설치합니다(아래 3번).

## 설치

1. 마켓플레이스 등록 (저장소 주소는 올린 곳에 맞게)

   ```
   /plugin marketplace add carina1754/ppt-skills
   ```

   GitHub가 아니면 git 주소를 그대로 씁니다: `/plugin marketplace add https://git.example.com/team/ppt-skills.git`. 비공개 저장소는 그 PC의 git이 이미 로그인되어 있어야 합니다. 받아 둔 폴더를 쓸 때는 `/plugin marketplace add C:\경로\ppt-skills`.

2. 플러그인 설치

   ```
   /plugin install ppt-maker@ppt-skills
   /plugin install frontend-slides@ppt-skills
   ```

3. (.pptx가 필요하면) 공식 문서 스킬

   ```
   /plugin marketplace add anthropics/skills
   /plugin install document-skills@anthropic-agent-skills
   ```

설치한 뒤 Claude Code를 다시 열면 "발표 자료 만들어줘", "이 내용으로 PPT", "장표 10장" 같은 말에 `ppt-maker`가 먼저 뜹니다.

### 프로젝트에 묶어 두기

팀 저장소의 `.claude/settings.json`에 아래를 넣으면, 그 저장소를 연 팀원에게 설치 안내가 자동으로 뜹니다.

```json
{
  "extraKnownMarketplaces": {
    "ppt-skills": {
      "source": { "source": "github", "repo": "carina1754/ppt-skills" }
    }
  },
  "enabledPlugins": {
    "ppt-maker@ppt-skills": true,
    "frontend-slides@ppt-skills": true
  }
}
```

GitHub가 아니면 `"source": { "source": "git", "url": "https://.../ppt-skills.git" }`.

## 새 판 받기

```
/plugin marketplace update ppt-skills
```

스킬을 고친 사람은 `plugins/ppt-maker/.claude-plugin/plugin.json`의 `version`을 올리고 커밋, 푸시합니다.

## 쓸 때 주의

- frontend-slides의 배포 단계(`deploy.sh`)는 Vercel에 **누구나 열 수 있는 주소**를 만듭니다. 회사, 고객사, 가격, 계약, 환자 정보가 든 자료는 배포하지 말고 PDF나 파일로 주고받습니다.
- PDF 내보내기(`export-pdf.sh`)는 처음 실행할 때 Playwright와 Chromium을 임시 폴더에 받습니다. .pptx 읽기(`extract-pptx.py`)는 `python-pptx`가 필요합니다.

## 출처와 라이선스

- `plugins/frontend-slides`: [zarazhangrui/frontend-slides](https://github.com/zarazhangrui/frontend-slides) 커밋 `9906a34`(2.1.0)를 그대로 가져왔습니다. MIT, 원문 라이선스는 `plugins/frontend-slides/LICENSE`. 원본 새 판을 받을 때는 그 폴더를 통째로 바꾸고 이 줄의 커밋을 고칩니다.
- `plugins/ppt-maker`: 팀에서 만든 것, MIT.
