# 개인정보 보호 반 전체 실시간 마인드맵

## 구조
가운데 `개인정보` → `조 이름` → `찾은 개인정보` → `보호 방법`

모든 학생이 같은 주소로 접속하면 반 전체가 같은 마인드맵을 실시간으로 봅니다.

예:
- `https://아이디.github.io/저장소이름/?class=1`
- `https://아이디.github.io/저장소이름/?class=2`

`?class=1`과 `?class=2`는 서로 다른 교실 데이터로 저장됩니다.

---

## 중요한 저장 방식

이 버전은 **마인드맵 전체를 한 번에 저장하지 않습니다.**

조 / 개인정보 / 보호 방법 **노드 하나마다 Firestore 문서 하나**로 저장합니다.

따라서:
- 1조가 자기 노드를 수정해도 2조의 내용이 사라지지 않습니다.
- 수정 시 해당 문서의 `text`, `updatedAt` 필드만 바꿉니다.
- 새 노드는 기존 데이터를 덮어쓰지 않고 별도 문서로 추가합니다.
- 여러 기기에서 동시에 추가·수정하면 `onSnapshot`으로 실시간 반영됩니다.
- 같은 노드 하나를 두 사람이 동시에 수정하면 마지막 저장 내용이 남습니다.

---

## 1. Firebase에서 익명 로그인 켜기

Firebase Console → **Authentication(인증)** → **로그인 방법** → **익명(Anonymous)** → 사용 설정

GitHub Pages에서 사용할 경우:

Authentication(인증) → **설정** → **승인된 도메인**

여기에 본인의 GitHub Pages 도메인을 추가합니다.

예:
`teacher123.github.io`

`https://` 또는 저장소 경로는 넣지 않습니다.

---

## 2. Firestore Database 만들기

Firebase Console → **Firestore Database** → 데이터베이스 만들기

리전은 가까운 지역을 선택합니다.

---

## 3. Firestore 규칙 적용

이 폴더의 `firestore.rules` 내용을 복사해서

Firestore Database → **규칙(Rules)**

에 붙여 넣고 **게시**합니다.

### 기존 단서 공유 사이트와 같은 Firebase 프로젝트를 쓴다면
기존 규칙을 전부 지우면 안 됩니다.

기존 `service cloud.firestore { ... }` 안에
이 프로젝트의 `match /classes/{classId}/nodes/{nodeId}` 규칙을
기존 규칙과 충돌하지 않게 통합해야 합니다.

가능하면 이 마인드맵용으로 새 Firebase 프로젝트를 만드는 것이 가장 단순합니다.

---

## 4. firebase-config.js 수정

Firebase Console → 프로젝트 설정 → 내 앱 → 웹 앱에서
`firebaseConfig` 값을 복사합니다.

`firebase-config.js` 파일의 예시 값을 본인 값으로 교체합니다.

---

## 5. GitHub에 올리기

새 GitHub 저장소를 만든 뒤 아래 파일을 루트에 올립니다.

- `index.html`
- `firebase-config.js`

`firestore.rules`와 `README.md`는 GitHub에 같이 보관해도 됩니다.

GitHub 저장소 → Settings → Pages → Deploy from a branch →
`main` / `(root)` 선택

생성된 Pages 주소로 접속합니다.

---

## 6. 반별 주소

1반:
`https://아이디.github.io/저장소/?class=1`

2반:
`https://아이디.github.io/저장소/?class=2`

3반:
`https://아이디.github.io/저장소/?class=3`

같은 반 학생들은 반드시 같은 `?class=` 값을 사용해야 합니다.

---

## 수정·삭제

노드를 클릭하면 왼쪽에 선택 정보가 표시됩니다.

- `선택한 노드 수정`
- `선택한 노드 삭제`

조를 삭제하면 그 조 아래의 개인정보와 보호 방법도 함께 삭제됩니다.
개인정보를 삭제하면 연결된 보호 방법도 함께 삭제됩니다.

가운데 `개인정보` 노드는 웹페이지에 고정된 중심 노드이므로 삭제되지 않습니다.

---

## 수업 전 테스트 권장

1. 컴퓨터에서 주소 접속
2. 휴대폰 또는 시크릿 창에서 같은 `?class=` 주소 접속
3. 한쪽에서 `1조` 추가
4. 다른 쪽 화면에 바로 나타나는지 확인
5. 다른 기기에서 `전화번호` 추가
6. 첫 화면에도 바로 나타나는지 확인
7. `전화번호`를 수정하고 다른 조 내용이 그대로인지 확인

상단에 `실시간 연결됨`이 표시되면 Firebase 연결과 익명 로그인이 정상입니다.
