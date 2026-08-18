# 리센느 파트 맞추기 게임 🎧 (v3 — 개인전/경쟁전 + 랭킹 보드)

노래가 흐르는 동안 가사가 한 줄씩 하이라이트되고, 그 파트를 부르는 멤버를
실시간으로 맞추는 게임입니다. 맞힌 가사는 멤버 색깔로 물들고,
틀리거나 놓쳐도 곡은 끝까지 진행되며 점수가 계속 쌓입니다.
다 같이 부르는 구간은 **떼창** 버튼으로 맞힙니다.

**v3 새 기능**
- **개인전**: 기록을 어디에도 남기지 않고 가볍게 플레이
- **경쟁전**: 닉네임을 입력하고 플레이하면 점수가 **온라인 랭킹 보드**에 등록
  (모든 플레이어가 같은 보드를 봅니다 — 아래 Firebase 설정 필요)
- **🏆 랭킹 보드**: 곡별 상위 20명 (점수 → 정답률 → 콤보 → 먼저 등록 순)
- 7곡 전곡에 1절 파트 데이터 포함 (가사·멤버는 리서치 기반, 타이밍은
  음원 자동 분석 초안 — 파트 에디터로 미세 조정 추천)

## 폴더 구조

```
rescene-part-game/
├── index.html          ← 게임 + 파트 에디터 + 랭킹 보드 (전부 이 파일 하나)
├── songs/
│   ├── uhuh.mp3  loveattack.mp3  pinball.mp3  newworld.mp3
│   ├── dejavu.mp3  runaway.mp3  prettygirl.mp3
└── img/
    ├── woni.jpg  liv.jpg  minami.jpg  may.jpg  zena.jpg
```

## 지금 바로 해볼 것

1. 폴더에서 터미널을 열고 `python3 -m http.server` 실행
2. 브라우저에서 `http://localhost:8000` 접속
3. 곡을 고르고 **개인전** → 바로 플레이!
   (**경쟁전**은 아래 Firebase 설정을 마쳐야 열립니다)

> ⚠️ 6곡(UhUh·LOVE ATTACK·Pinball·New World·Deja Vu·Runaway)의 타이밍은
> 음원을 자동 분석해 만든 **초안**입니다. 대체로 맞지만 파트 경계가 1~2초
> 어긋난 곳이 있을 수 있어요. 파트 에디터에서 실제로 들어보며 다듬어주세요.
> (Pretty Girl은 직접 만드신 데이터를 그대로 유지했습니다)

> index.html을 더블클릭(file://)으로 열면 브라우저가 mp3 로딩을 막을 수 있어요.
> 그 경우에도 **파트 에디터 → 파일 선택**으로 mp3를 불러오면 동작합니다.

## 진짜 데이터 만들기 — 파트 에디터 사용법

시작 화면 아래 **🛠 파트 에디터**로 들어가서:

1. **음원 불러오기** — 곡을 고르거나 [파일 선택]으로 mp3를 직접 불러옵니다.
2. **가사 입력** — 파트(부르는 멤버가 바뀌는 단위) 하나당 한 줄씩 입력하고
   [표 만들기]를 누릅니다. 1절까지만 만들어도 됩니다.
3. **타이밍 찍기** — 노래를 재생하면서 각 파트가 시작되는 순간마다
   [⏺ 여기부터 다음 파트] (또는 스페이스바)를 누릅니다.
   - 이전 파트의 끝시간은 자동으로 채워집니다.
   - 파트 뒤에 간주가 있으면 그 파트가 끝날 때 [⏹ 파트 끝]을 누르세요.
   - 0.75× 배속과 −5초 버튼으로 정밀하게 찍을 수 있고,
     표에서 숫자를 직접 수정하고 ▶로 구간을 확인할 수 있습니다.
4. **정답 지정** — 표의 드롭다운에서 각 파트를 부르는 멤버(또는 떼창)를 고릅니다.
5. **완성** —
   - [🎮 게임으로 바로 테스트]: 즉시 게임으로 플레이해봅니다 (새로고침 전까지 유지)
   - [📋 코드 생성] → 복사 → `index.html` 상단 `SONGS` 안 해당 곡의
     `parts: [ ... ]` 자리에 붙여넣으면 영구 저장됩니다.

## 새 곡 추가하기

1. mp3를 `songs/` 폴더에 넣습니다. 파일명은 영문 소문자로 (예: `uhuh.mp3`).
2. `index.html`의 `SONGS` 배열에 추가:
   ```js
   { id: "uhuh", title: "UhUh", audio: "songs/uhuh.mp3", parts: [] },
   ```
3. 에디터에서 그 곡을 선택해 파트를 만들고 코드를 붙여넣습니다.

## 설정 값

- `GRACE` (기본 1.0초): 파트가 끝난 뒤에도 답을 받아주는 여유 시간
- `MEMBERS`의 `color`: 멤버 색깔 (가사 색칠·버튼에 모두 반영)

## 경쟁전 랭킹 보드 설정 (Firebase, 5~10분)

경쟁전 기록은 **모든 플레이어가 공유하는 온라인 보드**에 저장됩니다.
GitHub Pages는 정적 호스팅이라 서버가 없으므로, 무료 Firebase Firestore를
백엔드로 씁니다. 이 게임 규모라면 무료 요금제(Spark)로 충분합니다.

- 개인전: 아무 기록도 저장하지 않음 (설정 없이도 항상 가능)
- 경쟁전: 점수가 Firestore에 저장되고 🏆 랭킹 보드에 표시
- 설정 전에는 경쟁전 버튼이 잠겨 있고 안내 문구만 나옵니다

### 1) Firebase 프로젝트 만들기

1. https://console.firebase.google.com → **프로젝트 추가** (이름 예: `rescene-game`)
2. 왼쪽 메뉴 **빌드 → Firestore Database → 데이터베이스 만들기**
   - 위치: `asia-northeast3 (서울)` 추천, **프로덕션 모드**로 시작

### 2) 보안 규칙 붙여넣기

Firestore → **규칙** 탭에 아래를 통째로 붙여넣고 **게시**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 랭킹 보드: 읽기는 모두 허용, 쓰기는 "형식이 올바른 새 기록 추가"만 허용
    match /scores/{songId}/entries/{entry} {
      allow read: if true;
      allow create: if
        request.resource.data.keys().hasOnly(['name','score','total','pct','combo','ts']) &&
        request.resource.data.name is string &&
        request.resource.data.name.size() >= 1 &&
        request.resource.data.name.size() <= 12 &&
        request.resource.data.score is int &&
        request.resource.data.score >= 0 &&
        request.resource.data.total is int &&
        request.resource.data.total > 0 &&
        request.resource.data.total <= 300 &&
        request.resource.data.score <= request.resource.data.total &&
        request.resource.data.pct is int &&
        request.resource.data.pct >= 0 &&
        request.resource.data.pct <= 100 &&
        request.resource.data.combo is int &&
        request.resource.data.combo >= 0 &&
        request.resource.data.combo <= 300 &&
        request.resource.data.ts == request.time;
      allow update, delete: if false; // 수정·삭제는 콘솔에서만
    }
  }
}
```

### 3) 웹 앱 등록 + 설정 복사

1. ⚙️ **프로젝트 설정** → **내 앱** → 웹(`</>`) 아이콘 → 앱 등록
2. 화면에 나오는 `const firebaseConfig = { ... }` 객체를 복사

### 4) index.html에 붙여넣기

✅ **이미 완료** — 이 저장소의 `index.html`에는 `rescene-part-game` 프로젝트의
config가 들어가 있습니다 (`const FIREBASE_CONFIG = { ... }`).
아직 안 했다면 **1) Firestore 데이터베이스 만들기**와 **2) 보안 규칙 게시**만
마치면 경쟁전·랭킹 보드가 바로 작동합니다. SDK는 npm 없이 gstatic CDN에서
로드되므로 빌드 과정이 필요 없습니다.

> `apiKey`는 비밀키가 아니라 프로젝트 식별자라서 공개 저장소에 올라가도
> 됩니다. 실제 보호는 위 Firestore 규칙이 담당합니다.
> 로그인 없이 닉네임만 받는 구조라 마음먹으면 점수 조작은 가능합니다 —
> 팬 게임 규모에선 보통 충분하고, 필요해지면 익명 로그인 + App Check를
> 추가하면 됩니다. (무료 한도: 하루 읽기 5만 / 쓰기 2만 회)

### 데이터 구조 (참고)

```
scores / {songId} / entries / 자동ID: { name, score, total, pct, combo, ts }
```

보드는 곡별 상위 50개를 받아 점수 → 정답률 → 콤보 → 먼저 등록한 순으로
정렬해 20명을 보여줍니다. 기록 정리는 Firebase 콘솔 → Firestore에서 직접.

## GitHub Pages 배포

저장소: https://github.com/HugeAppear/rescene-part-game

1. 이 폴더 내용을 커밋하고 푸시 (`index.html`, `README.md`, `songs/`, `img/`)
2. 저장소 Settings → Pages → Branch `main` 선택
3. https://hugeappear.github.io/rescene-part-game/ 에서 플레이

### ⚠️ 배포 전 꼭 읽어주세요 (음원 저작권)

`songs/` 안의 mp3는 **곡 전체 파일**입니다. 이대로 공개 저장소에 올리면
누구나 원본 mp3를 통째로 내려받을 수 있어서, 구매한 음원이라도 사실상
재배포가 됩니다. 파트 데이터가 1절까지만 있으므로, 게임에서 실제로 쓰는
구간(마지막 파트 끝 + 3초)까지만 자른 파일을 올리는 것을 강력히 권장합니다.
**앞부분(0초)부터 자르는 게 아니라 뒷부분만 잘라내는 것이라, 파트 타이밍은
수정 없이 그대로 유효합니다**:

```bash
# 예: 앞 90초만 남기기 (곡별 '마지막 파트 끝'은 index.html의 parts 참고)
ffmpeg -i songs/uhuh.mp3 -t 90 -c copy songs/uhuh_cut.mp3
mv songs/uhuh_cut.mp3 songs/uhuh.mp3   # 같은 이름으로 교체하면 코드 수정 불필요
```

✂️ **`songs_trimmed/` 폴더에 잘라둔 파일이 준비되어 있습니다** — 6곡을 각각
마지막 파트 끝 +3초에서 자르고 2초 페이드아웃을 넣은 버전입니다 (Pretty Girl은
파트가 곡 전체라 제외). 공개 배포 시 `songs_trimmed/`의 파일들을 `songs/`로
덮어쓰기만 하면 됩니다 (파일명이 같아서 코드 수정 불필요, 타이밍 그대로 유효).

## 주의사항

- 음원·사진·가사의 권리는 더뮤즈엔터테인먼트 및 각 저작권자에게 있습니다.
  비영리 팬 게임 범위를 지키고, 요청이 있으면 내릴 수 있게 운영해주세요.
- 가사 표시는 1절 등 필요한 만큼만 사용하는 것이 안전합니다.
