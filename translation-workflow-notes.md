# 챕터 번역 작업 절차 및 기술적 주의사항 (2장 작업에서 얻은 교훈)

이 문서는 2장(Data) 번역 과정에서 실제로 겪은 문제들과 그 해결책을 정리한 것이다. 3장부터는 이 절차를 그대로 따른다.

## 표준 작업 절차

1. `sources.md`에서 해당 챕터의 원문 링크를 확인한다.
2. 원문 전체를 반드시 **raw.githubusercontent.com**을 통해 가져온다. `lamethods.org`와 `github.com`(및 `api.github.com`)은 이 환경의 조직 egress 정책상 직접 접근이 막혀 있다(403). 예: 원문 qmd는 `https://raw.githubusercontent.com/lamethods/lamethods.github.io/master/book1/chapters/ch0X-xxx/chX-xxx.qmd`, 참고문헌은 같은 경로의 `references.bib`. WebFetch는 내용을 요약해버려서 verbatim 복사에 부적합하므로 사용하지 않는다.
3. 본문 설명 텍스트만 한국어로 번역하고, R 코드 청크·청크 옵션(`#|`)·tex 블록(`\begingroup` 등)은 원문 그대로 한 글자도 바꾸지 않고 복사한다.
4. `glossary.md`의 기존 표기를 따르고, 새 용어는 파일에 바로 반영하지 말고 표로 제안한 뒤 확인을 받는다.
5. 챕터 파일은 YAML 프런트매터 없이 `# 제목 {#sec-라벨}`로 시작한다.
6. 완성되면 채팅 다운로드가 아니라 **연결된 폴더 안의 실제 경로**(`chapters/<파일명>.qmd`)에 직접 저장한다.
7. `_quarto.yml`의 chapters 목록은 이미 22개 챕터 전부(placeholder 포함)로 구성되어 있으므로, 새로 등록할 필요 없이 기존 placeholder 파일을 덮어쓰기만 하면 된다.

## 표/그림 처리 원칙

- 원문이 인터랙티브 표(`rmarkdown::paged_table()`, `gt::gt_preview()` 등)나 실제 ggplot2 코드를 쓰고 있다면, 그 코드를 그대로 가져온다. 정적 이미지로 대체하거나 비슷해 보이는 코드를 지어내지 않는다.
- `github.com/lamethods/code` 저장소에는 **방법론 챕터(3장~21장, 폴더명 `ch03-intro-r` ~ `ch21-sem`)만** 코드가 있다. 2장(Data)처럼 이 저장소에 폴더가 없는 챕터는 원문 자체의 qmd 소스가 코드의 유일한 출처다. 다만 `api.github.com`과 `github.com`이 막혀 있어 이 저장소는 디렉터리 목록을 볼 수 없고 파일명을 정확히 알 때만 raw.githubusercontent.com으로 개별 파일을 받을 수 있다. 원문 qmd 자체에 코드가 전부 인라인으로 들어있는 챕터(예: 3장)는 이 저장소를 따로 대조할 필요가 없다.
- Quarto 크로스레퍼런스 라벨(`#| label: fig-xxx` / `tbl-xxx`)은 사용자가 원하는 이름으로 바꿔도 되지만, 바꾸면 본문 안의 `@fig-xxx` 참조도 반드시 같이 고쳐야 한다.
- 원문 코드가 못 찾겠으면 절대 비슷하게 지어내지 말고 "못 찾았다"고 솔직히 알린다.
- 원문이 정적 이미지(`![캡션](images/xxx.png)`)를 쓰는 챕터는 아래 "이미지·보조 파일 폴더 구조" 절의 규칙을 따라 이미지를 받아온다. 캡션 텍스트는 번역 대상(본문)이므로 한국어로 옮기고, 파일 경로와 `{#fig-xxx ...}` 속성은 그대로 둔다.

## 참고문헌(인용) 처리 원칙

- 챕터 파일에는 YAML 프런트매터가 없으므로 Quarto의 `bibliography:`/`@key` 자동 인용 처리가 작동하지 않는다(루트의 `references.bib`/`references.qmd`는 현재 어떤 챕터에도 연결되어 있지 않다). 따라서 원문의 `[@key]` 인용은 해당 챕터 안에서 **처음 등장하는 순서대로 1, 2, 3...으로 번호를 매겨** `\[n\]` (역슬래시로 이스케이프한 대괄호)로 바꾸고, 문서 맨 끝에 `## 참고문헌`이라는 제목으로 그 챕터만의 번호 목록을 직접 작성한다.
- 각 참고문헌 항목은 챕터별 `references.bib`(예: `.../ch0X-xxx/references.bib`)에서 해당 키의 필드(author, year, title, journal/publisher, volume/number/pages 등)를 가져와 "저자 이니셜 (연도) 제목. 저널명 권(호):쪽수." 또는 "저자 (연도) 제목. 출판사" 형식(2장·3장에서 이미 쓰인 반코버 스타일)으로 직접 조합해서 적는다. 자동 렌더링에 의존하지 않는다.
- 같은 문장에서 원문이 여러 문헌을 함께 인용하면([@a; @b]) 그 챕터 안에서의 첫 등장 순서에 따라 연속 번호(`\[11–12\]`)로 표기한다(2장 사례 참고).

## 이미지·보조 파일 폴더 구조 (3장 작업 이후 정리된 규칙)

챕터 폴더(`chapters/`) 안에는 성격이 다른 두 종류의 하위 폴더가 생길 수 있는데, 반드시 구분해서 관리한다.

1. **`chapters/<챕터파일명>_images/`** — 원문에서 가져온 정적 스크린샷·삽화 등, **사람이 직접 관리하는 소스 이미지**를 담는 폴더다. 챕터 파일 이름과 접두어를 맞춰 챕터별로 분리한다(예: `chapters/R기초_images/rstudio_ui.png`, qmd에서는 `R기초_images/rstudio_ui.png`로 상대경로 참조). 이렇게 하면 어떤 이미지가 어떤 챕터 것인지 폴더명만 보고 바로 알 수 있고, 나중에 챕터를 삭제/이동할 때 이미지도 통째로 같이 옮기면 된다.
   - 예전에 있던 이름 없는 공용 폴더 `chapters/ images`(공백으로 시작하는 이름, 빈 폴더)는 어떤 챕터에서도 쓰이지 않아 3장 작업 중 삭제했다. 앞으로 새 공용/이름 없는 이미지 폴더를 만들지 말고, 항상 챕터별 `_images` 폴더를 새로 만든다.
2. **`chapters/<챕터파일명>_files/`** — Quarto가 **렌더링할 때 자동으로 생성**하는 빌드 산출물 폴더다(예: `데이터_files/figure-html/...`, htmlwidgets 의존 라이브러리 등). 이 폴더 안에는 절대 수동으로 파일을 추가하거나 편집하지 않는다. 렌더링할 때마다 새로 만들어지므로, 사라져도 다시 `quarto render`/`quarto preview`를 돌리면 재생성된다.
   - 3장 작업 중 `chapters/데이터_files/figure-epub/` 폴더를 발견해 삭제했다. 이는 `_quarto.yml`에서 EPUB 포맷이 비활성화되기 *이전*에 만들어진 낡은 산출물로, `figure-html`이나 `libs` 폴더 없이 EPUB 전용 그림만 들어 있어 현재 HTML(GitHub Pages) 사이트와는 무관했다. 앞으로 EPUB을 다시 활성화하지 않는 한 이런 `figure-epub` 하위 폴더는 다시 생기지 않아야 정상이며, 혹시 다시 나타나면 안전하게 지워도 된다(다음 HTML 렌더링이 필요한 파일을 알아서 다시 만든다).
3. **보조 R 함수 스크립트**는 챕터마다 새로 만들지 않고 `chapters/aux_functions.R` 하나에 계속 이어서 추가한다(자세한 이유는 "알려진 기술적 함정" 1, 2번 참고). 3장 작업 중 이 파일과 동일한 초기 버전이었던 `chapters/_aux.R`(원문 `aux.R`을 그대로 옮겨온 것으로, `rep()` 이름 충돌 버그가 그대로 남아 있고 어떤 qmd에서도 참조되지 않던 죽은 파일)과, 프로젝트 루트에 남아 있던 `Claude outputs/_aux.R`(그 사본)을 정리했다. 앞으로 이런 임시/사본 스크립트를 프로젝트 루트나 챕터 폴더에 남기지 않도록 주의한다.

## 알려진 기술적 함정

1. **Windows 예약어 파일명 금지**: `CON`, `PRN`, `AUX`, `NUL`, `COM1`~`COM9`, `LPT1`~`LPT9`는 확장자와 무관하게 Windows에서 파일명으로 쓸 수 없다. 원문 저장소의 보조 스크립트가 `aux.R`이라는 이름이면 반드시 다른 이름(예: `aux_functions.R`)으로 바꿔서 저장하고, `source("aux.R")` 호출부도 함께 고쳐야 한다. (2장에서 이미 `chapters/aux_functions.R`로 만들어 둠 — `sum_and_hist`, `preview_cols`, `summary_df`, `fix_decimal_sep` 함수 포함. 원문 그대로였던 임시 사본 `_aux.R`은 3장 작업 중 정리 완료.)
2. **R 기본 함수 이름과 충돌 금지**: 원문의 `aux.R`에는 원래 `rep`이라는 이름의 커스텀 함수가 있었는데, 이게 R 기본 함수 `rep()`을 덮어써서 Quarto/knitr 내부에서 코드 펜스를 만들 때 쓰는 `rep()` 호출이 깨졌다("사용되지 않은 인자 (l)" 에러). 그래서 `fix_decimal_sep`으로 이름을 바꿨다. 새 챕터의 보조 함수도 `rep`, `filter`, `select` 등 흔한 base R/tidyverse 함수 이름과 겹치지 않는지 미리 확인할 것.
3. **작업 디렉터리**: `_quarto.yml`에 `execute-dir: project`가 없으므로, 각 챕터의 R 코드는 기본적으로 **그 qmd 파일이 있는 폴더**(`chapters/`)를 작업 디렉터리로 실행된다. `source()`나 상대경로(이미지 경로 포함)는 프로젝트 루트가 아니라 `chapters/` 기준으로 잡아야 한다.
4. **git 명령은 Claude가 직접 실행하지 않는다**: 이 환경의 device_bash 셸은 파일 삭제 권한이 기본적으로 꺼져 있어서(필요할 때 사용자 승인을 받아 그 세션에서만 켤 수 있음), git이 스스로 만드는 `.git/index.lock`을 정리하지 못하고 매번 남겨버릴 수 있다. 이게 남아있으면 RStudio Git 탭 체크박스가 먹통이 된다. 그러므로 git status/add/commit은 항상 사용자가 RStudio Terminal/Git 탭에서 직접 실행하고, Claude는 파일 내용만 다룬다(파일/폴더 삭제가 꼭 필요할 때는 사용자 승인을 받은 뒤 device_bash로 지우고, 커밋은 사용자가 한다).
5. **EPUB 포맷은 현재 비활성화**: `_quarto.yml`의 `epub:` 설정은 2장의 인터랙티브 표와 호환 문제로 주석 처리해 뒀다. 새 챕터도 인터랙티브 위젯(paged_table 등)을 쓰면 EPUB에서 문제가 될 수 있으니, 지금은 HTML(GitHub Pages)만 신경 쓰면 된다. EPUB이 비활성화된 상태에서 렌더링해도 `<챕터>_files/figure-epub/` 같은 낡은 산출물이 과거에 생성되어 남아있을 수 있으니(3장 작업 중 발견·삭제), 가끔 `chapters/*_files/`를 점검해서 현재 쓰는 포맷과 무관한 하위 폴더가 없는지 확인하면 좋다.
6. **파일 검증**: 새로 만들거나 수정한 파일은 채팅 첨부(SendUserFile) + `device_commit_files`로 연결된 폴더에 반영하고, 가능하면 device_bash로 실제 저장된 내용을 다시 읽어 확인한다. `device_commit_files`가 알 수 없는 이유로 실패하면(`path changed between validation and write` 등) 예약어 파일명 문제일 가능성을 먼저 의심한다. 이미지 등 바이너리 파일은 전송 후 크기/해시가 원본과 다를 수 있는데(3장에서 PNG에 약 5.7KB가 더해지는 현상 관찰 — 색상 프로필 등 메타데이터 추가로 추정), PNG 헤더/IEND가 정상이고 실제로 열어봤을 때 시각적으로 문제가 없다면 무해한 것으로 판단하고 넘어가도 된다.
7. **참고문헌 자동 처리는 꺼져 있음**: 위 "참고문헌(인용) 처리 원칙" 참고 — `bibliography:`/`@key` 자동 렌더링에 의존하지 말고 항상 챕터별로 번호를 수동으로 매길 것.

## 렌더링 검증 순서 (챕터 완성 후 사용자에게 안내)

1. RStudio에서 해당 챕터 파일만 Render 버튼으로 먼저 테스트 (또는 `quarto preview chapters/<파일>.qmd --to html --no-watch-inputs --no-browse`)
2. 문제없으면 `quarto render`로 책 전체 테스트
3. 그다음 `quarto publish gh-pages`로 배포
