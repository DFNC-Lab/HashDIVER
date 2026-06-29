# HashDIVER

DIVER = Data Integrity Verifier & Equivalence Reporter

HashDIVER compares the data in two folders, or across several endpoints, to check
whether files are byte-for-byte identical and, where the bytes differ, whether they
are still logically equivalent under a format-aware comparison. Results are written
to an Excel workbook and shown as charts.

HashDIVER는 두 폴더 또는 여러 단말에 담긴 데이터를 비교해서, 파일이 바이트 단위로
동일한지, 그리고 바이트가 다를 때는 형식을 고려한 비교에서 논리적으로 동등한지까지
확인합니다. 결과는 엑셀 파일과 차트로 정리됩니다.

Every comparison falls into one of four verdicts. / 비교 결과는 네 가지로 나뉩니다.

| Verdict | Meaning | 의미 |
|---------|---------|------|
| Integrity-preserving | Byte-for-byte identical | 바이트 단위로 완전히 동일 |
| Logically equivalent | Equal under format-aware logical comparison such as text normalization, JSON and XML, SQLite, image perceptual hashing, media sampling, and LevelDB | 형식 인식 논리 비교에서 동등 — 텍스트 정규화, JSON/XML, SQLite, 이미지 지각 해시, 미디어 샘플링, LevelDB 등 |
| Different | A real difference in content | 실질적인 내용 차이 |
| Inconclusive | Not comparable, errored, or transient; excluded from the rates | 비교 불가·오류·일시적 산출물, 비율 계산에서 제외 |

---

## 1. Getting started / 시작하기

HashDIVER ships as a single executable, so no Python or extra libraries are needed.
It runs on 64-bit Windows.

HashDIVER는 단일 실행 파일이라 Python이나 추가 라이브러리를 설치할 필요가 없습니다.
64비트 Windows에서 동작합니다.

1. Download the latest `HashDIVER.exe` from the Releases page.
   Releases 페이지에서 최신 `HashDIVER.exe`를 내려받습니다.
2. Double-click to run.
   더블클릭해서 실행합니다.

Video and audio sample comparison needs ffmpeg on your PATH. Without it, only those
items are marked "not performed" and every other comparison still runs.

동영상·오디오 샘플 비교에는 ffmpeg가 PATH에 있어야 합니다. 없으면 그 항목만
"not performed"로 표시되고 나머지 비교는 그대로 진행됩니다.

---

## 2. Folder layout / 폴더 구조

Put a single root folder on top. Inside it, one folder per application; inside each
application, one folder per endpoint; inside each endpoint, a `db_compare` and/or
`media_compare` folder.

루트 폴더 하나를 맨 위에 둡니다. 그 안에 앱마다 폴더 하나, 각 앱 안에 엔드포인트마다
폴더 하나, 각 엔드포인트 안에 `db_compare` 또는 `media_compare` 폴더를 둡니다.

```
<root>\                                 
  ├─ Discord\
  │     ├─ Mobile(A)\
  │     │      ├─ db_compare\            ← app data, e.g. users/appdata/*  (DB, SQLite, LevelDB ... )
  │     │      └─ media_compare\         ← media the user downloaded (images, video, audio ... ) 
  │     ├─ PC1(W)\     { db_compare\ , media_compare\ }
  │     ├─ PC2(W)\     { db_compare\ , media_compare\ }
  │     └─ PC3(M)\     { db_compare\ , media_compare\ }
  ├─ LINE\            same endpoint layout / 엔드포인트 구성 동일
  └─ Teams\           …                  one folder per app / 앱마다 폴더 하나
```

Within each application, PC1 is the baseline and is compared one-to-one against
every other endpoint. For Discord that means PC1(W)→Mobile(A), PC1(W)→PC2(W), and
PC1(W)→PC3(M).

각 앱에서 PC1을 기준으로 나머지 엔드포인트와 1대1로 비교합니다. Discord라면
PC1(W)→Mobile(A), PC1(W)→PC2(W), PC1(W)→PC3(M) 입니다.

---

## 3. Using the GUI / GUI 사용법

1. Run `HashDIVER.exe`.
   `HashDIVER.exe`를 실행합니다.
2. Choose your root folder under Root folder.
   Root folder에서 위 구조의 루트 폴더를 선택합니다.
3. Click Run comparison. Progress is shown in the log below.
   Run comparison을 누릅니다. 진행 상황은 아래 로그에 표시됩니다.
4. When it finishes, a timestamped folder appears next to the program with the result Excel file inside.
   끝나면 프로그램 옆에 타임스탬프 폴더가 생기고 그 안에 결과 엑셀 파일이 들어갑니다.
5. Review the results in the tabs.
   결과는 탭에서 확인합니다.

### Result tabs / 결과 탭

| Tab | Contents | 내용 |
|-----|----------|------|
| Summary | Overall and per-application summary with the headline metrics | 전체 및 앱별 요약과 핵심 지표 |
| Comparable by app | Per-application verdict share for comparable records | 비교 가능한 레코드의 앱별 판정 비율 |
| Logically equivalent detail | Which kind of equivalence backed each verdict | 어떤 종류의 동등성으로 판정됐는지 |
| ONLY_IN asymmetry | Left and right imbalance of files present on only one side | 한쪽에만 있는 파일의 좌우 불균형 |
| Pairwise heatmap | Verdict-rate heatmap across source pair and application | 소스쌍과 앱에 걸친 판정 비율 히트맵 |
| Rows | Every compared file with its verdict, paths, and diff evidence | 모든 비교 파일의 판정, 경로, 차이 근거 |

### Copying results / 결과 복사

- Copy summary on the Summary tab lays the items out as `label: value` with `----` dividers.
  Summary 탭의 Copy summary는 항목을 `라벨: 값`과 `----` 구분선으로 정리합니다.
- Copy on the Rows tab, or Ctrl+C on a selection, separates the columns with `|`.
  Rows 탭의 Copy, 또는 선택 후 Ctrl+C는 열을 `|` 로 구분합니다.

---

## 4. Troubleshooting / 문제 해결

- Windows shows a "protected your PC" or "unknown publisher" warning: the executable is unsigned. Click More info, then Run anyway. If antivirus quarantines it, add an exception.
  Windows가 "PC 보호" 또는 "알 수 없는 게시자" 경고를 띄울 때: 서명이 없는 실행 파일이라 그렇습니다. 추가 정보 → 실행을 누르세요. 백신이 격리하면 예외로 등록합니다.

- "Could not find any db_compare / media_compare folders": check the layout in section 2. Each endpoint folder needs a `db_compare` or `media_compare` folder, and an application needs at least two endpoints to compare, for example PC1(W) and PC2(W).
  "Could not find any db_compare / media_compare folders": 2번 폴더 구조를 확인하세요. 각 엔드포인트 폴더에 `db_compare` 또는 `media_compare` 폴더가 있어야 하고, 한 앱에 비교할 엔드포인트가 둘 이상 있어야 합니다. 예를 들어 PC1(W)와 PC2(W)입니다.
  
- Video or audio shows "not performed": install ffmpeg and ffprobe and add them to your PATH. On Windows, add the `/bin` folder from https://www.gyan.dev/ffmpeg/builds/ to PATH.
  동영상이나 오디오가 "not performed"로 나올 때: ffmpeg와 ffprobe를 설치하고 PATH에 등록하세요. Windows는 https://www.gyan.dev/ffmpeg/builds/ 의 `/bin` 폴더를 PATH에 추가합니다.

---

## 5. Citation / 인용

HashDIVER accompanies a paper that is currently under review. Full citation details
and a BibTeX entry will be added here once the paper is published.

HashDIVER는 현재 심사 중인 논문과 함께 공개되는 도구입니다. 논문이 게재되면 정식 인용
정보와 BibTeX를 여기에 추가합니다.

---

## 6. License / 라이선스

```
SPDX-FileCopyrightText: 2026 Soojin Kang, Kookmin University
SPDX-License-Identifier: Apache-2.0
```

See LICENSE for the full terms and NOTICE for the copyright notice. You are free to
use, modify, and redistribute HashDIVER; include the copyright notice and a copy of
the license when you do.

LICENSE 파일에서 전체 약관을, NOTICE 파일에서 저작권 고지를 확인할 수 있습니다.
자유롭게 사용·수정·재배포할 수 있으며, 그럴 때 저작권 고지와 라이선스 사본을 함께
포함해 주세요.
