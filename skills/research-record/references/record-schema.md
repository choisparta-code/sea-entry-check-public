# 기록 카드 스키마 (sea-entry-record/2)

형식 예시이며 실제 조사 결과가 아니다.

세금·지급·절차가 있는 카드의 선택 필드 `resultView`는 `../../../references/result-view.md`를 따른다. 수치 표는 metrics, 지급표는 resultView.payments, 일반 절차 그림은 resultView.procedures(기존 visa도 호환), 규정과 별도의 사례 재검토는 checklist/conflicts/nextActions로 보존한다. 미정 금액을 0이나 가상 기본값으로 채우지 않는다.

체크리스트 항목의 선택 필드 `legalEvidence`는 질문별 법률 근거 배열이다(항목당 최대 30개). 이전 카드에는 생략 가능하며 새 업무 검토 질문에는 빈 배열을 사용한다. 근거마다 sourceRef(출처 번호 또는 null), provision(조항), originalText(짧은 원문), summary(한국어 요지), effectiveFrom/To(효력 시작/종료일), checkedAt(확인일), currencyStatus, applicability, conditions, exceptions, applicationReason, openQuestions(문자열 배열)를 기록한다.

- 날짜는 YYYY-MM-DD 또는 빈 문자열이다. 시작일보다 이른 종료일은 거부한다. 현행 표시의 확인일은 효력 기간 안에 있어야 한다.
- currencyStatus: 미확인, 현행, 시행예정, 개정 확인 필요, 폐지, 충돌.
- applicability: 미확인, 적용, 해당없음, 충돌.
- 원문 발췌는 최대 2000자, 나머지 설명은 최대 5000자. 남은 질문은 최대 100개, 각 2000자 이하.
- 연결한 sourceRef는 같은 항목의 sourceRefs에도 포함해야 한다. 출처 미확인 초안은 null을 쓴다.
- 장부를 사용하는 항목의 확인은 비어 있지 않은 장부, A/B 출처의 주소·현행 효력·확인일, 조항·요지·시행일·근거 확인일·조건·예외·적용 이유, 현행/적용 상태, 남은 질문 없음이 필요하다. 입력 요건 충족은 사실성·법적 적용 보증이 아니다.
- 변경된 근거 내용을 먼저 저장하고 검토 상태를 별도로 저장한 뒤 질문을 확인한다. 이미 장부를 사용하는 질문에서 필드를 생략하여 제거하지 않는다. 최신 get_research 기록과 expectedRevision으로 수정한다.

초안 예: `"legalEvidence":[{"sourceRef":null,"provision":"","summary":"원문 검토 전","currencyStatus":"미확인","applicability":"미확인","openQuestions":["현행 조문 확인"]}]`.

`workflow`는 선택 필드다. 일반 조사나 기존 카드에는 생략할 수 있다. 선택한 업무의 알고 있는 상황 답변만 넣으며, 미확인 값은 빈 문자열 또는 답변 키 생략으로 표현한다. 서버는 빠진 답변을 빈 문자열로 정규화한다. 답변은 항목당 2000자 이하 문자열이며 아래 허용 키 밖의 필드는 거부한다.

| kind | 업무 | answers 허용 키 |
|---|---|---|
| assignment | 주재원·파견 | purpose, origin, relationship, assignmentType, location, nationality, stay, payer, costBearer, residency, benefits, insurance, referencePeriod |
| hire | 직원 채용·급여 | purpose, relationship, location, nationality, stay, payer, costBearer, residency, benefits, insurance, referencePeriod |
| payment | 해외 용역비·로열티 | purpose, payer, recipient, incomeType, location, costBearer, referencePeriod |
| profit | 사업 소득·이익 송금 | purpose, businessType, location, incomeType, costs, referencePeriod |

assignmentType은 빈 문자열(미확인), intraCompany(기업 내 전근), localEmployment(현지 고용), serviceSupply(용역 수행), laborSupply(인력 공급), businessTrip(출장), other 중 하나다. 그 외 답변은 사실관계를 설명하는 자유 문자열이다. 원문·개인 식별번호를 불필요하게 저장하지 않는다. payer는 지급자, costBearer는 최종 비용 부담자다. 체류 일수나 지급국만으로 면세·거주자 여부를 확정하지 않는다. 검토 대상 기간은 referencePeriod에 기록한다.

예: `"workflow": {"kind":"assignment","answers":{"payer":"한국 본사","costBearer":"현지 법인","assignmentType":"intraCompany"}}`. 이 답변은 법률 검증 결과가 아니다. 국가 또는 workflow가 달라지면 기존 결론 확정이 해제된다.

```json
{
  "schema": "sea-entry-record/2",
  "country": "VN",
  "industry": "화장품 유통",
  "topic": "수입 화장품 등록 절차",
  "date": "2026-10-09",
  "status": "조사중",
  "checklist": [
    {
      "area": "업종별 인허가",
      "item": "화장품 수입·유통 등록",
      "status": "미확인",
      "note": "실제 자료 확인 전",
      "sourceRefs": [1]
    }
  ],
  "sources": [
    {
      "id": 1,
      "title": "문서 또는 페이지 제목",
      "org": "발행 기관",
      "year": "2025",
      "url": "https://example.com/",
      "tier": "D",
      "checkedAt": "",
      "effectiveStatus": "미확인"
    }
  ],
  "conflicts": [
    "충돌 내용을 한 문장으로"
  ],
  "metrics": [{"name":"시장 규모","value":null,"unit":"USD","period":"미확인","definition":"범위 확인 필요","sourceRefs":[],"verificationStatus":"미확인","note":""}],
  "conclusion": "",
  "conclusionConfirmed": false,
  "lessons": [],
  "nextActions": [],
  "tags": []
}
```

## 필드 규칙

| 필드 | 값 |
|---|---|
| country | `VN`(베트남), `ID`(인도네시아) |
| status | `조사중`, `검토완료`, `보류` |
| checklist[].status | `확인`, `미확인`, `해당없음`, `충돌` |
| sources[].tier | `A`, `B`, `C`, `D` |
| date | `YYYY-MM-DD` 형식 |
| sourceRefs | 연결된 sources의 id 배열. 없으면 [] |
| conclusion | 사용자가 확정한 결론만. 아니면 빈 문자열 |
| lessons | 사용자가 말했거나 동의한 배운 점만 |

배열 필드는 비어 있어도 `[]`로 둔다. 문자열 필드가 비면 빈 문자열을 쓴다. JSON 안에 주석을 넣지 않는다.

출처 id는 중복 없는 양의 정수다. '확인' 항목에는 출처가 필요하며, '확인' 지표는 숫자 값도 필요하다. 미확인 값은 null, 실제 0은 숫자 0으로 구분한다. metrics.verificationStatus는 확인/미확인/충돌이다. 충돌하는 값은 별도 지표로 각각의 출처·정의·기간을 보존한다. 환산 시 원래 값, 환율 출처·기준일을 note에 적는다.

서버가 id/provider/runId/revision/createdAt/updatedAt/history를 관리한다. 신규 카드에서 생성하지 않는다. 기존 기록 수정 시 get_research의 최신 id와 revision을 사용한다. 선택 날짜 checkedAt/reviewAt은 YYYY-MM-DD 또는 빈 문자열이다.

MCP 초안 저장의 conclusion은 빈 문자열, conclusionConfirmed는 false다. 검토완료는 확정 결론이 있어야 한다. 수동 JSON 가져오기에서만 사용자 명시 동의를 받은 결론을 포함할 수 있다.

전체 백업은 sea-entry-workbench/2이며 records/runs/events를 포함한다. 기존 sea-entry-record/1, 카드 배열, sea-entry-records/1 백업도 가져올 수 있다. v1 sourceRef는 sourceRefs로 변환하고 기존 이력·시각은 유지한다. 같은 ID의 다른 내용은 덮어쓰지 않고 전체 가져오기를 취소한다.
