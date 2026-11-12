# 짧은 링크용 Firestore 규칙 (프로젝트 mysonglist-e19ac)

Firebase 콘솔 > Firestore Database > 규칙 에서, 기존 `songapp` 규칙은 그대로 두고
`match /databases/{database}/documents { ... }` 안에 아래를 **추가**한 뒤 게시한다.

```
    // 애창곡 리스트: 짧은 공유 링크 (번호를 아는 사람만 1건씩 읽기, 목록 나열은 불가)
    match /shares/{id} {
      allow get: if true;
      allow list: if false;
      allow create: if request.auth != null
        && request.resource.data.keys().hasOnly(['code','t','uid'])
        && request.resource.data.uid == request.auth.uid
        && request.resource.data.code is string
        && request.resource.data.code.size() <= 900000;
      allow update: if false;
      allow delete: if request.auth != null && resource.data.uid == request.auth.uid;
    }
```

규칙을 넣기 전에는 앱이 알아서 긴 주소로 대신 보낸다("공유 서버 설정이 아직 안 돼 있어 긴 주소로 보내요" 안내).
