
# 📑 API Documentation of Identificati0n.com 

# 1. 연락처
**텔레그렘 공지 채널**: https://t.me/identificati0ncom 
**API 도입 문의**: https://t.me/amkakxk11x

# 2. API 타입
- **Common Types**:
  ```ts
  enum IdentificationType {
    "SMS" = "SMS",
    "PASS" = "PASS"
  }

  enum Gender {
    "MAN" = "MAN",
    "WOMAN" = "WOMAN"
  }

  export type MobileCarrier = "KT" | "SKT" | "LGU" | "MVNO_KT" | "MVNO_SKT" | "MVNO_LGU";

  type APIProviderId = "SCI" | "DANAL" | "KMC" | "KMC2" | "NICE2";
  ```
  - `KMC, KMC1, NICE2`은 PASS인증을 제외한, SMS 인증만 지원합니다.

- **Request Headers**:
  ```ts
  type RequestHeaders = {
    Authorization: string
  }
  ```

- **Common Failure Response Types**
  ```ts
  type CommonFailureResponse = {
    success: false,
    message: string
  }
  ```

---

## GET `/api-providers`
사용 가능한 인증 API 제공업체를 조회합니다.  

**Response**:
```ts
type APIProvidersResponse = {
    id: APIProviderId;
    supportedIdentificationTypes: IdentificationType[];
}[]
```

---

## POST `/:apiProvider/request`
인증 요청을 생성합니다.

**Payload Type**:
```ts
type VerificationRequestPayload = {
  identificationType: IdentificationType;
  carrier: MobileCarrier;
  name: string; // e.g. "홍길동"
  phone: string; // e.g. "01012345678"
  birthdate: string; // 2001년 1월 1일생 기준 e.g. "20010101" 또는 "010101"
  gender: string | Gender; // birthdate가 8자리: "MAN" | "WOMAN" & birthdate가 6자리: "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8"
  isForeigner?: boolean; // birthdate가 8자리일 떄 required.
  identifierAlias?: string; // 추후 verify에서 사용자 식별을 위한 정보. e.g. 113272558293939930 (디스코드 아이디) 또는 nec2nice (다른 식별 가능 정보)
  customMessageHeader?: string; // 선택 (Pro 전용)
}
```

**Response Type**:
```ts
type VerificationRequestResponse = {
  success: true,
  message: "Request has been created.",
  expirationTime: 60000,
  taskId: string,
  verificationContext: {
    mobileCarrier: MobileCarrier,
    identificationType: IdentificationType,
    name: string,
    phone: string,
    birthdate: string, // e.g. "2001-02-05T00:00:00.000Z"
    gender: Gender,
    isForeigner: boolean
  },
  identifierAlias: string | "UNKNOWN"
}
```

---

## POST `/verify`
인증 코드 혹은 PASS 앱 인증 여부를 확인합니다.

**Payload Type**:
```ts
type VerifyIdentityRequestPayload = {
  taskId: string; // /:apiProvider/request 에서 반환된 taskId
  smsCode?: string // identificationType이 SMS일 떄 required. 
}
```

**Response Type**:
```ts
type VerifyIdentityRequestResponse = {
  success: true,
  message: string,
  verificationContext: {
    mobileCarrier: MobileCarrier,
    identificationType: IdentificationType,
    name: string,
    phone: string,
    birthdate: string, // e.g. "2001-02-05T00:00:00.000Z"
    gender: Gender,
    isForeigner: boolean
  },
  identifierAlias: string | "UNKNOWN"
}
```
