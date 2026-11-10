# 김정묵 포트폴리오 배포용 코드

확정한 포트폴리오 화면과 문구를 React + TypeScript + Vite 프로젝트로 정리했습니다. 프로젝트 상세, 연구 탭, 모바일 메뉴와 브라우저 사진 미리보기를 포함합니다. 서버나 API 키 없이 정적 사이트로 배포할 수 있습니다.

## 들어 있는 파일

- `src/App.tsx`: 소개, 프로젝트 상세와 연구 내용
- `src/styles.css`: 브라운 색상, 반응형 레이아웃과 애니메이션
- `public/images/`: 기웃, 대전광, 이미지 분할 파이프라인 원본 이미지
- `public/fonts/`: 한글 웹 글꼴과 라이선스
- `public/portfolio.pdf`: A4 13쪽 제출용 포트폴리오
- `public/resume.docx`: 기존 이력서 다운로드 파일
- `vercel.json`: Vercel 빌드 설정
- `.github/workflows/deploy-pages.yml`: GitHub Pages 수동 배포 설정

## 내 컴퓨터에서 열기

Node.js 22.13 이상을 사용합니다. 압축을 푼 `portfolio-deploy` 폴더에서 실행합니다.

```bash
npm ci
npm run dev
```

터미널에 표시되는 로컬 주소를 엽니다. 정적 빌드 결과를 확인할 때는 아래 명령을 사용합니다.

```bash
npm run build
npm run preview
```

빌드 결과는 `dist/`에 생성됩니다. `index.html`을 파일 탐색기에서 직접 여는 대신 위 로컬 서버 주소를 사용합니다.

## 1. GitHub 저장소 만들기

1. GitHub에서 새 저장소를 만듭니다. 이름은 예를 들어 `portfolio`로 정합니다.
2. 아래 명령을 사용한다면 README, `.gitignore`, 라이선스를 자동 생성하지 않은 빈 저장소로 만듭니다.
3. 압축을 푼 폴더에서 실행합니다. 마지막 주소의 `YOUR_USERNAME`과 `portfolio`를 본인의 계정과 저장소 이름으로 바꿉니다.

```bash
git init
git add .
git commit -m "Add portfolio"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/portfolio.git
git push -u origin main
```

GitHub 로그인은 본인 컴퓨터의 Git 또는 GitHub Desktop에서 진행합니다. `.gitignore`가 의존성, 빌드 결과와 환경 파일을 제외합니다. 압축파일에 기존 Git 저장소 이력과 서비스 인증 정보는 들어 있지 않습니다.

## 2. Vercel에 배포하기

1. Vercel에 GitHub 계정으로 로그인합니다.
2. **Add New → Project**에서 방금 만든 저장소를 **Import**합니다.
3. 아래 값을 확인하고 **Deploy**를 누릅니다.

| 설정 | 값 |
| --- | --- |
| Framework Preset | Vite |
| Root Directory | `package.json`이 있는 폴더. 압축을 푼 내용 그대로 올렸다면 저장소 루트 |
| Install Command | `npm ci` 또는 기본값 |
| Build Command | `npm run build` |
| Output Directory | `dist` |
| Node.js | 22.x 이상 |
| 환경변수 | 별도로 필요 없음 |

`vercel.json`에 프레임워크와 빌드 결과 경로를 넣어 두었습니다. 배포가 끝나면 Vercel이 표시하는 주소를 사용합니다. 이후 연결된 운영 브랜치에 변경사항을 push하면 다시 배포됩니다. Vercel에서 배포할 때 `BASE_PATH`는 지정하지 않습니다.

## 3. GitHub Pages에 배포하기

GitHub에 코드를 저장하는 것과 사이트를 공개하는 GitHub Pages는 별도 설정입니다.

1. 저장소의 **Settings → Pages**로 이동합니다.
2. **Build and deployment → Source**에서 **GitHub Actions**를 선택합니다.
3. **Actions**에서 **Deploy portfolio to GitHub Pages**를 선택합니다.
4. **Run workflow → main → Run workflow**를 누릅니다.
5. 작업이 완료되면 배포 작업의 URL 또는 **Settings → Pages**에 표시된 주소를 엽니다.

워크플로가 Pages에서 받은 `base_path`로 빌드하기 때문에 `계정.github.io/저장소이름/` 형태에서도 이미지·글꼴·다운로드 경로를 맞춥니다. 이 저장소는 두 플랫폼 중 원하는 곳에서만 실행할 수 있도록 Pages 워크플로를 **수동 실행**으로 두었습니다. Pages를 갱신하려면 push 후 **Run workflow**를 다시 누릅니다.

push할 때 자동 배포하려면 `.github/workflows/deploy-pages.yml`의 `on` 설정을 다음처럼 바꿉니다.

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
```

## 내용과 사진 바꾸기

- 본문은 `src/App.tsx`에서 수정합니다. 프로젝트는 상단의 `projects` 배열, 연구는 `research===0`부터 `research===3`까지의 영역입니다.
- 공개 사진은 `public/images/`에 넣고 `src/App.tsx`의 `Photo` 컴포넌트에서 해당 파일을 연결합니다. GitHub에 commit하고 다시 배포해야 다른 방문자에게도 보입니다.
- 화면의 **사진 바꾸기**는 현재 브라우저의 미리보기 기능입니다. 파일을 서버에 업로드하거나 모든 방문자의 사진을 바꾸지는 않습니다.
- 프로필 사진과 이미지 분할 카드 사진은 원래 화면처럼 `[사진]` 자리로 남아 있습니다.
- 기존 이력서 버튼은 `public/resume.docx`를 내려받습니다. PDF로 바꾸려면 `App.tsx`에서 두 곳의 `asset("/resume.docx")`를 `asset("/portfolio.pdf")`로 바꾸고 버튼 문구를 원하는 표현으로 수정합니다.
- 제출용 PDF를 나중에 수정하면 `public/portfolio.pdf`도 새 파일로 교체합니다.

## 확인한 범위

- TypeScript 검사 및 Vite 정적 빌드
- 루트 경로와 `/portfolio/` 하위 경로 빌드
- 확정한 프로젝트·연구 내용과 삭제 요청 유지
- PDF의 한글 글꼴 포함, 텍스트 추출과 페이지 렌더링

실제 Vercel 및 GitHub Pages의 계정 연결·배포 실행은 본인 계정에서 위 절차로 진행합니다.

## 공식 문서

- [Vercel의 Git 저장소 연결](https://vercel.com/docs/git)
- [Vite on Vercel](https://vercel.com/docs/frameworks/frontend/vite)
- [Vite 정적 사이트 배포](https://vite.dev/guide/static-deploy)
- [Vite 하위 경로와 정적 자산](https://vite.dev/guide/build#public-base-path)
- [GitHub Pages 사용자 지정 워크플로](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
