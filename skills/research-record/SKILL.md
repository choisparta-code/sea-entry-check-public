---
name: research-record
description: 베트남·인도네시아 진출 조사 결과를 이력 웹페이지에 쌓을 기록 카드로 정리한다. 사용자가 "기록 카드 만들어줘", "이력에 남겨줘", "조사 결과 저장", "결론 기록", "이력 관리에 넣을 형식으로", "research record" 같은 요청을 할 때, 또는 entry-checklist나 verified-research 결과를 남기겠다고 할 때 사용한다.
---

# 조사 기록 카드

이 대화의 조사 결과를 기록 카드로 정리한다. 스키마는 `references/record-schema.md`를 따른다. MCP 연결 시 `../../references/live-workflow.md`에 따라 도구로 저장하고, 연결이 없으면 JSON을 웹페이지의 "가져오기"에 붙여넣도록 제공한다.

## 절차

먼저 `../../references/practical-output.md`를 읽고 세금·절차·근거·재검토 결과가 기록에서 빠지지 않도록 해당 스키마 필드에 보존한다. 사용자의 결론 확정은 근거 없는 수치나 법률 적용을 검증된 사실로 바꾸지 않는다. 수동 JSON에도 같은 기준을 적용한다.

세금·지급·절차 결과를 남길 때는 `../../references/result-view.md`를 읽고 웹 표시용 `metrics`와 해당하는 `resultView.payments`·`resultView.procedures`, 적용 재검토 `checklist`를 함께 저장한다. 채팅의 표·Mermaid나 note만 저장하면 웹 지급표·절차 그림으로 이어지지 않는다. 부족한 입력은 계산 보류와 필요한 자료로 남긴다.

1. 대화에서 나온 내용만 모은다: 국가, 업종, 주제, 체크리스트 항목과 상태, 출처, 충돌, 다음 할 일.
2. MCP의 `save_research`는 초안 저장용이다. `conclusion`은 빈 문자열, `conclusionConfirmed`는 false, `status`는 "조사중"으로 둔다. 최종 결론은 웹 화면에서 사용자가 직접 입력·확정한다. JSON 수동 가져오기에서는 사용자가 명시적으로 확정한 문장만 conclusion에 넣고 conclusionConfirmed를 true로 기록할 수 있다. 후보는 대화에서 별도로 제안한다.
3. `lessons`(배운 점)는 사용자가 말했거나 동의한 것만 쓴다. 예를 들어 "출처가 충돌할 때는 통계 당국 값을 기준으로 삼기로 했다"처럼 다음 조사에 쓸 수 있는 문장이다. 지어내지 않는다. 후보를 제안한 뒤 확인받는다.
4. 출처 번호는 `sources`의 `id`와 체크리스트·시장 지표의 `sourceRefs` 배열이 일치해야 한다. 미확인 수치는 `null`로 두며 값·단위·기준 기간·정의·검증 상태를 `metrics`에 보존한다.
5. 확인하지 못한 내용은 빈 값이나 "미확인"으로 둔다.
6. MCP 저장 성공 후 `get_research`로 다시 읽어 수치·지급표·절차 단계·근거·재검토가 보존됐는지 확인하고 반환된 기록 ID와 화면, 미확인 항목을 안내한다. 도구 없이 작성했다면 코드 블록 하나로 JSON을 출력하고 가져오기 방법과 비어 있는 필드를 안내한다. 저장 도구가 실패하면 실패를 밝히고 JSON을 제공한다. 실제 성공 응답 없이 저장됐다고 말하지 않는다.

## 안내 문구

MCP 저장에 성공하면 도구 응답의 dashboardUrl을 안내한다. 연결 없이 JSON을 제공할 때는 공개 작업실 https://sea-research-workbench.choisparta.workers.dev/workspace 에 로그인한 뒤 "가져오기"에 붙여넣을 수 있다. 사용자가 별도의 로컬 작업실을 지정했다면 그 주소를 쓴다. JSON 제공만으로 기록이 저장됐다고 안내하지 않는다.
