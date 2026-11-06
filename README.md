# My Portfolio: 손민균 개인 소개 웹사이트

Hugo와 [hugo-paper](https://github.com/nanxiaobei/hugo-paper) 테마로 만든 개인 포트폴리오 사이트입니다. GitHub Actions로 GitHub Pages에 자동 배포됩니다.

- 사이트: <https://microbot125.github.io/MyWebPage/>
- 저장소: <https://github.com/microbot125/MyWebPage>
- Actions 실행 기록: <https://github.com/microbot125/MyWebPage/actions>

> **작성 표기**: 이 README는 AI(Claude Code, 모델 `claude-opus-5-5`)가 초안을 작성했고, 저장소 주인 손민균이 검수했습니다. (초안 작성일 2026-11-06)

---

## 1. 폴더 구조

배포에 실제로 쓰이는 폴더는 **`site/`뿐입니다.**

```
.
├── site/                      ← 실제 사이트 (빌드·배포 대상)
│   ├── hugo.toml              ← 사이트 설정 (제목, 이름, 소개, 메뉴, SNS)
│   ├── pages.json             ← 배포용 설정 (baseURL, 테마 경로; 수업 제공 스크립트가 생성)
│   ├── content/               ← 페이지와 글 (Markdown)
│   │   ├── about.md           ← 소개
│   │   ├── contact.md         ← 연락
│   │   └── post/              ← 활동 글 (홈 목록에 표시)
│   ├── static/                ← 이미지 등 정적 파일 (avatar.jpeg)
│   └── themes/template/       ← hugo-paper 테마 사본 (일부 수정, 6절 참고)
├── .github/
│   ├── workflows/hugo-pages.yml  ← 배포 워크플로
│   └── pages_build.py            ← 워크플로가 실행하는 빌드 스크립트
├── .github-pages.json         ← 배포 준비 상태 기록 (Hugo 버전, baseURL)
├── prepare_github_pages.py    ← 수업 제공 배포 준비 스크립트 (최초 1회 실행)
└── (그 외 assets/, layouts/, exampleSite/ 등)
                               ← 원본 테마 ZIP 그대로. 배포에 쓰이지 않음
```

루트의 `layouts/`, `assets/`, `exampleSite/`를 고쳐도 사이트에는 반영되지 않습니다.

## 2. 필요한 도구와 버전

| 도구 | 배포(GitHub Actions) | 로컬 확인에 쓴 버전 | 비고 |
|---|---|---|---|
| Hugo **extended** | 0.167.0 | 0.169.0 (Homebrew) | 필수. 워크플로에서 버전 지정 |
| Python | 3.12 | 3.13.9 | 빌드 스크립트 실행용 (3.9 이상) |
| Git | - | 2.56.0 | commit·push |
| Node.js / Go / Dart Sass | 24 / 1.27.1 / 1.105.0 | 불필요 | 워크플로가 자동 설치. 이 테마는 실제로 쓰지 않음 |

### 설치 (macOS 기준)

```bash
brew install hugo      # extended 버전이 설치됨
hugo version           # "+extended" 표시 확인
```

다른 OS는 [Hugo 설치 안내](https://gohugo.io/installation/)를 따릅니다. 반드시 **extended** 버전을 설치합니다.

## 3. 로컬에서 사이트 확인

```bash
git clone https://github.com/microbot125/MyWebPage.git
cd MyWebPage/site
hugo server --config hugo.toml,pages.json
```

- 브라우저에서 <http://localhost:1313/MyWebPage/>를 엽니다. baseURL에 `/MyWebPage/` 경로가 있어서 로컬에서도 이 경로가 붙습니다.
- 파일을 저장하면 자동으로 새로고침됩니다. 종료는 `Ctrl + C`입니다.
- `--config hugo.toml,pages.json`은 반드시 함께 지정합니다. `pages.json`에 테마 경로가 들어 있습니다.
- 초안(`draft = true`) 글까지 보려면 `-D`를 붙입니다.

### 배포와 같은 방식으로 빌드하기

```bash
# 저장소 루트(MyWebPage/)에서 실행
python3 .github/pages_build.py --build
```

GitHub Actions와 같은 명령으로 `public/`에 결과물을 만들고, 하위 경로(`/MyWebPage/`) 보정까지 합니다. `public/`은 `.gitignore`로 제외되어 있습니다.

## 4. 내용 수정 방법

| 바꾸고 싶은 것 | 수정할 파일 |
|---|---|
| 사이트 제목, 이름, 한 줄 소개, 저작권 표기 | `site/hugo.toml` (`title`, `[params]`의 `name`·`bio`, `copyright`) |
| 상단 메뉴 | `site/hugo.toml`의 `[menu]` |
| GitHub·Instagram 아이콘 링크 | `site/hugo.toml`의 `[params]` `github`, `instagram` |
| 프로필 이미지 | `site/static/avatar.jpeg` 교체 (파일명을 바꾸면 `hugo.toml`의 `avatar`도 수정) |
| 소개 페이지 | `site/content/about.md` |
| 연락 페이지 | `site/content/contact.md` |
| 활동 글 추가 | `site/content/post/`에 새 `.md` 파일 작성 (아래 형식) |

활동 글 형식:

```toml
+++
title = "글 제목"
date = "2026-11-06"        # 오늘 이전 날짜여야 빌드에 포함됨
description = "한 줄 설명"
+++

본문 (Markdown)
```

- 파일 이름이 주소가 됩니다. 예: `post/핀테크 공모전.md` → `/MyWebPage/post/핀테크-공모전/`
- `draft = true`이거나 날짜가 미래면 배포 사이트에 나오지 않습니다.

## 5. 배포와 확인 절차

1. 수정한 파일을 commit하고 `main` 브랜치에 push합니다.
   ```bash
   git add <수정한 파일>
   git commit -m "변경 내용을 설명하는 메시지"
   git push origin main
   ```
2. GitHub 저장소의 **Actions** 탭에서 **Deploy Hugo to GitHub Pages** 워크플로가 자동으로 시작되는지 확인합니다.
3. `build`와 `deploy` 작업이 모두 초록색 체크(success)가 될 때까지 기다립니다. 보통 1~2분 걸립니다.
4. <https://microbot125.github.io/MyWebPage/>를 열어 변경 내용을 확인합니다.
   - GitHub Pages는 최대 10분 동안 캐시(`cache-control: max-age=600`)를 씁니다. 바뀐 내용이 안 보이면 **새로고침**하거나 강력 새로고침(`Cmd + Shift + R`), 시크릿 창으로 확인합니다.
5. 실패하면 Actions 실행 기록의 로그에서 실패한 단계를 확인합니다.

최초 설정: GitHub 저장소 **Settings → Pages → Build and deployment → Source**를 **GitHub Actions**로 지정해야 합니다(설정 완료).

## 6. 원본 테마에서 수정한 부분

`site/themes/template/` 아래 테마 파일 중 다음을 수정했습니다. 수정은 모두 AI(Claude Code)의 제안·작성을 본인이 검토하고 브라우저로 확인한 뒤 반영했습니다.

| 파일 | 수정 내용 | 커밋 |
|---|---|---|
| `layouts/partials/mermaid.html` | mermaid CDN 버전 고정 (`mermaid@12.3.0`) | `5ff9b6e` |
| `layouts/partials/header.html` | 다크모드·메뉴 버튼을 `<button>`으로 바꿔 키보드로 조작 가능하게 함, 한국어 `aria-label` | `a5ba7e9` |
| `layouts/_default/baseof.html` | `avatar`에 사이트 내부 이미지 경로(`avatar.jpeg`)를 쓸 수 있게 함 | `9dcc3bd` |
| `layouts/partials/header.html` | 닫힌 모바일 메뉴로 Tab 초점이 들어가지 않게 함 (`inert`) | `5276cd8` |
| `layouts/partials/header.html` | SNS 아이콘 경로를 절대 경로로 바꿔 내부 페이지에서도 표시 | `ff4a083` |
| `layouts/partials/header.html` | 모바일 메뉴를 연 채 창을 넓히면 스크롤이 잠기던 문제 수정 | `5428455` |
| `layouts/_default/list.html` | 홈의 이름 표시를 `<h1>`으로 바꿔 문서 제목 구조 보완 | `927d1b0` |

## 7. 출처와 라이선스

### 사용한 외부 자료

| 자료 | 출처 | 라이선스 | 사용 위치 |
|---|---|---|---|
| hugo-paper 테마 (v6.31) | <https://github.com/nanxiaobei/hugo-paper> | MIT (© 2018 南小北) | `site/themes/template/` (라이선스: `site/themes/template/LICENSE`, 루트 `LICENSE`) |
| 테마 포함 아이콘·이미지 (github·instagram SVG, 다크모드 아이콘, favicon) | hugo-paper 테마 | MIT (테마에 포함) | `site/themes/template/static/` |
| highlight.js v9.15.6 | <https://highlightjs.org/> | BSD-3-Clause | `site/themes/template/static/highlight.min.js` (테마에 포함) |
| Hugo | <https://gohugo.io/> | Apache 2.0 | 사이트 생성기 |
| 배포 준비 스크립트 | 수업 제공 | 수업 자료 | `prepare_github_pages.py`, `.github/pages_build.py`, `.github/workflows/hugo-pages.yml` |

- 사이트 푸터의 `powered by hugo`, `hugo-paper` 출처 링크는 유지합니다.
- KaTeX·mermaid는 테마가 CDN에서 불러오는 기능이지만, 현재 글에서는 사용하지 않습니다.

### 직접 제작 / 외부 / AI 구분

| 구분 | 대상 |
|---|---|
| **직접 작성** | `site/content/`의 모든 글 (소개, 연락, 활동 글 3개), `site/hugo.toml` 설정 값 |
| **직접 제공** | 프로필 이미지 `site/static/avatar.jpeg` |
| **외부 자료** | 위 표의 테마, 아이콘, 스크립트 |
| **AI 활용** | 6절의 테마 수정 코드 (Claude Code 작성, 본인 검토·확인) / 이 README 초안 (Claude Code 작성, 본인 검수) / 핀테크 공모전 활동의 인터랙션 웹 (Claude로 제작, 이 저장소에는 포함되지 않음) |

AI 수정 기록의 상세 내용(수정 전후 코드, 이유)은 보고서의 AI 활용 기록에 정리했습니다.
