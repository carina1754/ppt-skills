# ppt-skills

발표 자료와 영상을 만들 때 쓰는 Claude Code 스킬 묶음입니다. 저장소 하나가 그대로 플러그인 마켓플레이스라서, 팀원은 명령 몇 줄로 설치하고 `/plugin marketplace update` 한 번으로 새 판을 받습니다.

## 들어 있는 것

| 플러그인 | 하는 일 |
|---|---|
| `ppt-maker` | 팀 공용 절차. 청중과 목적 정리, 주장 제목 개요를 먼저 확인받고, 형식을 고르고(HTML 슬라이드, .pptx, 영상), 한국어 글꼴과 표기, AI 티 점검표, 모든 장을 그림으로 떠서 확인 |
| `frontend-slides` | HTML 슬라이드 생성기. 스타일 미리보기 3개 중 고르기, 템플릿 모음, 기존 .pptx를 웹 슬라이드로 변환, PDF 내보내기 |
| `hyperframes` | HTML로 영상 만들기(HeyGen 오픈소스). 소개 영상, 자막, 애니메이션 차트, 세로 영상, 발표자 노트가 있는 발표 모드 슬라이드쇼 |

고칠 수 있는 .pptx가 필요하면 Anthropic 공식 `pptx` 스킬도 같이 설치합니다(아래 3번).

## 설치

1. 마켓플레이스 등록

   ```
   /plugin marketplace add carina1754/ppt-skills
   ```

2. 플러그인 설치 (필요한 것만 골라도 됩니다)

   ```
   /plugin install ppt-maker@ppt-skills
   /plugin install frontend-slides@ppt-skills
   /plugin install hyperframes@ppt-skills
   ```

   이미 HeyGen 마켓플레이스에서 `hyperframes@hyperframes`를 설치했다면 `hyperframes@ppt-skills`는 건너뜁니다. 둘 다 켜면 같은 스킬이 두 번 뜹니다.

3. (.pptx가 필요하면) 공식 문서 스킬

   ```
   /plugin marketplace add anthropics/skills
   /plugin install document-skills@anthropic-agent-skills
   ```

4. (영상을 만들면) 처음 한 번 Node.js 22 이상과 FFmpeg 설치

   ```
   winget install OpenJS.NodeJS.LTS
   winget install Gyan.FFmpeg
   ```

   macOS는 `brew install node ffmpeg`. 설치 뒤 터미널을 새로 열고 `node -v`, `ffmpeg -version`으로 확인합니다.

설치한 뒤 Claude Code를 다시 엽니다.

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
    "frontend-slides@ppt-skills": true,
    "hyperframes@ppt-skills": true
  }
}
```

## 사용법

설치하면 평소 말로 부탁해도 맞는 스킬이 알아서 뜹니다. 확실히 부르고 싶으면 `/ppt-maker`, `/frontend-slides`, `/hyperframes`처럼 슬래시로 부릅니다.

### 1. 발표 자료 (ppt-maker)

이렇게 부탁합니다.

- "첨부한 기획서로 경영진 보고용 발표 자료 10장 만들어줘"
- "이 회의록으로 고객사 제안 장표 만들어줘, 파워포인트에서 고칠 수 있게"
- "기존 deck.pptx를 웹 슬라이드로 다시 디자인해줘"

진행 순서는 이렇습니다.

1. **질문**: 청중, 목적, 발표용인지 읽기용인지, 장 수, 형식을 묻습니다. 자료에 이미 있으면 묻지 않습니다.
2. **개요 확인**: 장마다 주장 제목, 주인공(숫자, 그래프, 사진 등), 배치를 표로 보여 줍니다. 순서나 메시지는 여기서 고치는 것이 가장 쌉니다.
3. **형식별 만들기**: HTML은 스타일 미리보기 3개 중에서 고르고, .pptx는 pptx 스킬로 처음부터 만듭니다.
4. **확인과 보고**: 모든 장을 그림으로 떠서 넘침과 겹침을 보고, 점검표를 돌린 뒤 파일 위치와 채울 칸을 알려 줍니다.

잘 쓰는 법은 이렇습니다.

- 원문 문서, 숫자, 사진, 화면 캡처를 같이 줍니다. 받은 자료에 없는 숫자는 지어내지 않고 `[ ]`로 비워 두므로, 그 칸은 직접 채웁니다.
- 회사 서식(.potx, .pptx), 로고, 색이 있으면 같이 줍니다. 그것이 기본 디자인보다 먼저입니다.
- 고칠 때는 장 번호를 짚어 말합니다: "4장 그래프를 막대로, 7장은 빼줘".

### 2. HTML 슬라이드 (frontend-slides)

- "이 내용으로 HTML 슬라이드 만들어줘" 또는 `/frontend-slides`
- 결과는 `.html` 파일 하나입니다. 브라우저로 열고 방향키로 넘깁니다. 인터넷이 없으면 글꼴만 기본 글꼴로 바뀝니다.
- PDF가 필요하면 "PDF로도 뽑아줘"라고 합니다. 처음에 Playwright와 Chromium을 임시 폴더에 받습니다.

### 3. 영상 (hyperframes)

이렇게 부탁합니다.

- "이 발표 자료 내용으로 1분짜리 소개 영상 만들어줘, 1920×1080, 한국어 자막 포함"
- "제품 출시 30초 세로 영상(1080×1920) 만들어줘"
- "이 표 데이터로 막대가 자라는 애니메이션 차트 영상"
- 장표를 만든 뒤라면: "방금 개요로 장면당 6초짜리 영상 만들어줘"

진행 순서는 이렇습니다.

1. 프로젝트 폴더를 만들고 장면을 HTML로 씁니다.
2. 브라우저 미리보기 주소를 알려 주고 확인을 기다립니다. 고칠 곳은 "3초쯤 나오는 제목 더 크게"처럼 시점을 짚어 말합니다.
3. 확인하면 MP4로 렌더하고, 길이와 프레임을 확인한 뒤 파일 위치를 알려 줍니다.

직접 돌릴 때는 프로젝트 폴더에서 아래 명령을 씁니다.

```
npx hyperframes preview                                  # 브라우저 미리보기
npx hyperframes render --quality draft -o draft.mp4      # 빠른 확인용
npx hyperframes render --quality delivery -o final.mp4   # 최종본
npx hyperframes doctor                                   # Node, FFmpeg, Chrome 점검
```

라이브 발표(발표자 노트, 단계별 드러내기)는 "발표 모드 슬라이드쇼로 만들어줘"라고 하면 `slideshow` 스킬로 만들고 `npx hyperframes present`로 띄웁니다.

## 새 판 받기

```
/plugin marketplace update ppt-skills
```

스킬을 고친 사람은 `plugins/ppt-maker/.claude-plugin/plugin.json`의 `version`을 올리고 커밋, 푸시합니다. `hyperframes`는 HeyGen 원본 저장소를 바로 가리키므로 따로 고칠 것이 없습니다.

## 쓸 때 주의

- **바깥으로 나가는 기능**: frontend-slides의 배포(`deploy.sh`)는 Vercel에 **누구나 열 수 있는 주소**를 만듭니다. hyperframes의 `cloud render`, `publish`, HeyGen 음성과 아바타 생성은 내용을 HeyGen 서버로 보냅니다. 회사, 고객사, 가격, 계약, 환자 정보가 든 자료는 로컬에서만 만들고 파일로 주고받습니다.
- **hyperframes 사용 통계**: 렌더 뒤 사용 통계를 보냅니다(프로젝트 이름과 영상 내용은 빠짐). 끄려면 `npx hyperframes telemetry disable`.
- **.pptx 읽기**: `extract-pptx.py`는 `python-pptx`가 필요합니다.

## 출처와 라이선스

- `plugins/frontend-slides`: [zarazhangrui/frontend-slides](https://github.com/zarazhangrui/frontend-slides) 커밋 `9906a34`(2.1.0)를 그대로 가져왔습니다. MIT, 원문 라이선스는 `plugins/frontend-slides/LICENSE`. 원본 새 판을 받을 때는 그 폴더를 통째로 바꾸고 이 줄의 커밋을 고칩니다.
- `hyperframes`: [heygen-com/hyperframes](https://github.com/heygen-com/hyperframes)를 마켓플레이스에서 가리키기만 하고 이 저장소에는 복사하지 않았습니다. Apache-2.0.
- `plugins/ppt-maker`: 팀에서 만든 것, MIT.
