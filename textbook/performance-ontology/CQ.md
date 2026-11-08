# 역량 질문 (Competency Questions)

공연/전시 서비스 온톨로지가 **답할 수 있어야 하는 질문**을 정리한 문서입니다.
교재 3.4절의 2단계(범위 결정)에 해당하며, 7단계(검증)에서 이 질문들로 온톨로지를 시험합니다.

- 대상 파일: `protege_ontology.owl`
- 방법론: Ontology Development 101 (Noy & McGuinness, 2001)

---

## 1. 역량 질문

| ID | 질문 | 난이도 | 확인하려는 것 |
|---|---|---|---|
| CQ1 | 공연 X의 출연자는 누구인가? | 쉬움 | 기본 관계 하나 (출연) |
| CQ2 | 공연장 Y에서 열리는 뮤지컬은 무엇인가? | 보통 | 클래스(뮤지컬)와 관계(공연 장소)의 조합 |
| CQ3 | 가격이 5만 원 이하인 티켓이 있는 공연은 무엇인가? | 어려움 | 티켓과 공연의 연결 + 값 조건 |

---

## 2. 질문별로 필요한 요소

질문의 **명사는 클래스**, **동사는 객체 속성**, **조건은 데이터 속성**이 됩니다.

| ID | 필요한 클래스 | 필요한 객체 속성 | 필요한 데이터 속성 | 현재 파일 |
|---|---|---|---|---|
| CQ1 | Performance, Artist | hasPerformer (공연 → 아티스트) | hasTitle | ✅ 모두 있음 |
| CQ2 | Musical, Venue | takesPlaceAt (공연 → 장소) | hasTitle | ✅ 모두 있음 |
| CQ3 | Ticket, Performance | **ticketFor (티켓 → 공연)** | hasPrice | ❌ ticketFor 없음 |

---

## 3. 빠진 요소 → 다음 마일스톤에서 추가할 것

### M2. 클래스·속성 추가
| 추가할 것 | 종류 | 정의역 (Domain) | 치역 (Range) | 이유 |
|---|---|---|---|---|
| ticketFor | 객체 속성 | Ticket | Performance | CQ3: 어느 공연의 티켓인지 연결 |

### M3. 제약 (카디널리티)
| 클래스 | 제약 | 의미 | 관련 슬라이드 |
|---|---|---|---|
| Performance | takesPlaceAt **exactly 1** Venue | 공연 장소는 정확히 1곳 | 28쪽 5단계, 42쪽 OWL |
| Performance | hasPerformer **min 1** Artist | 공연에는 출연자가 최소 1명 | 28쪽 5단계 |
| Ticket | ticketFor **exactly 1** Performance | 티켓 한 장은 공연 하나에 대한 것 | CQ3 |

---

## 4. 검증용 데이터 (M4에서 입력할 인스턴스)

| 인스턴스 | 클래스 | 속성 값 |
|---|---|---|
| 오페라의유령 | Musical | hasTitle "오페라의 유령", hasPerformer 배우A, takesPlaceAt 샤롯데씨어터 |
| 레미제라블 | Musical | hasTitle "레미제라블", hasPerformer 배우B, takesPlaceAt 블루스퀘어 |
| 배우A | Artist | — |
| 배우B | Artist | — |
| 샤롯데씨어터 | Venue | — |
| 블루스퀘어 | Venue | — |
| 티켓1 | Ticket | ticketFor 오페라의유령, hasPrice 45000 |
| 티켓2 | Ticket | ticketFor 레미제라블, hasPrice 120000 |

---

## 5. 기대하는 답 (M5 검증 기준)

| ID | 구체적인 질문 | 기대하는 답 |
|---|---|---|
| CQ1 | 오페라의 유령의 출연자는? | 배우A |
| CQ2 | 샤롯데씨어터에서 열리는 뮤지컬은? | 오페라의 유령 |
| CQ3 | 5만 원 이하 티켓이 있는 공연은? | 오페라의 유령 (티켓1, 45,000원) |

세 질문 모두 기대한 답이 나오면 온톨로지가 요구사항을 충족한 것으로 봅니다.
M5에서는 이 질문들을 SPARQL(또는 Protégé의 DL Query)로 바꿔 실행합니다.

---

## 6. 진행 상황

- [x] M1. 역량 질문 정하기
- [ ] M2. 빠진 클래스·속성 추가 (ticketFor)
- [ ] M3. 제약 추가 (카디널리티 3개)
- [ ] M4. 인스턴스 입력
- [ ] M5. 검증 (HermiT 추론 + CQ 질의)
- [ ] M6. 문서화 (한글 라벨·설명)
- [ ] M7. 시연 준비
