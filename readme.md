
# 📑 Identification API Docs

## 도입 문의
Telegram **@identificati0ncom || @sunr1s2_0**

---

## 기본 Request 정보

- **API 제공업체 (apiProviders)**:
  - `"DANAL"` | `"SCI"` | `"NICE2"` | `"KG"` | `"KMC1"`
  - `NICE2, KG, KMC1` 은 현재 SMS 인증만 지원합니다.
  - `KG` 에서는 시범적으로 커스텀 헤더 전송을 지원합니다. (Pro 전용, 시범적으로 적용되었습니다.)

- **Authorization**:
  - Header
  - - `Authorization`: `{Access Token}`

- **Responses**
  - **성공 Response**
  ```json
  {
    "success": true,
    "message": "string"
  }
  ```

  - **실패 Response**:
  ```json
  {
    "success": false,
    "message": "string"
  }
  ```

---

## API Endpoint 목록

## 본인인증 요청
---
### GET `/api-providers`
사용 가능한 인증 API 제공업체 및 인증 지원 타입(supportedIdentificationTypes) 조회  

**Response**:
```json
[
  {
    "id": "DANAL",
    "supportedIdentificationTypes": ["SMS", "PASS"] // S
  },
  ...
]
```

---

### POST `/:apiProvider/request`
인증 요청을 생성합니다.

**Payload (JSON)**:
```json
{
  "identificationType": "SMS" | "PASS",
  "name": "string",
  "phone": "01012345678",
  "birthdate": "20010205" 또는 "010205",
  "gender": "1~8" (6자리 생일) | "MAN" | "WOMAN" (8자리 생일),
  "isForeigner": true | false, // (8자리 생일일 때 필요)
  "carrier": "KT" | "SKT" | "LGU" | "MVNO_KT" | "MVNO_SKT" | "MVNO_LGU",
  "identifierAlias": "string", // 선택
  "customMessageHeader": ""    // 선택 (Pro 전용)
}
```

**Response**:
```json
{
  "success": true,
  "message": "Request has been created.",
  "expirationTime": 60000,
  "taskId": "string",
  "verificationContext": {
    "mobileCarrier": "KT",
    "identificationType": "SMS",
    "name": "홍길동",
    "phone": "01012345678",
    "birthdate": "2001-02-05T00:00:00.000Z",
    "gender": "MAN",
    "isForeigner": false
  },
  "identifierAlias": "디스코드ID 또는 UNKNOWN"
}
```

---

### POST `/verify`
인증 코드 혹은 PASS 앱 인증 여부를 확인합니다.

**Payload (JSON)**:
```json
{
  "taskId": "string", // /:apiProvider/request 에서 생성된 taskId를 입력하셔야 합니다.
  "smsCode": "123456" // SMS 방식일 때만 필수
}
```

**Response**:
```json
{
  "success": true,
  "message": "Verification succeeded.",
  "verificationContext": {
    "mobileCarrier": "KT",
    "identificationType": "SMS",
    "name": "홍길동",
    "phone": "01012345678",
    "birthdate": "2001-02-05T00:00:00.000Z",
    "gender": "MAN"
  },
  "identifierAlias": "디스코드ID"
}
```