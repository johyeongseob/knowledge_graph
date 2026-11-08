# 역량 질문 (Competency Questions)

공연/전시 서비스 온톨로지가 **답할 수 있어야 하는 질문**을 정리한 문서입니다.
교재 3.4절의 2단계(범위 결정)에서 질문을 정하고, 7단계(검증)에서 이 질문들로 온톨로지를 시험합니다.

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

| ID | 클래스 | 객체 속성 | 데이터 속성 |
|---|---|---|---|
| CQ1 | Performance, Artist | hasPerformer (공연 → 아티스트), 역관계 performsIn | hasTitle |
| CQ2 | Musical, Venue | takesPlaceAt (공연 → 장소) | hasTitle |
| CQ3 | Ticket, Performance | ticketFor (티켓 → 공연) | hasPrice |

---

## 3. 검증용 데이터

| ID | 한글 라벨 | 클래스 | 속성 값 |
|---|---|---|---|
| CharlotteTheater | 샤롯데씨어터 | Venue | — |
| BlueSquare | 블루스퀘어 | Venue | — |
| ActorA | 배우A | Artist | — |
| ActorB | 배우B | Artist | — |
| PhantomOfTheOpera | 오페라의 유령 | Musical | hasTitle "오페라의 유령", hasPerformer ActorA, takesPlaceAt CharlotteTheater |
| LesMiserables | 레미제라블 | Musical | hasTitle "레미제라블", hasPerformer ActorB, takesPlaceAt BlueSquare |
| Ticket1 | 티켓1 | Ticket | ticketFor PhantomOfTheOpera, hasPrice 45000 |
| Ticket2 | 티켓2 | Ticket | ticketFor LesMiserables, hasPrice 120000 |

8개 개체는 모두 서로 다른 개체(owl:AllDifferent)로 선언되어 있습니다.

---

## 4. 기대하는 답과 검증 결과

| ID | 구체적인 질문 | 기대하는 답 | DL Query | SPARQL (Snap SPARQL) | 배포본 SPARQL (Comunica) |
|---|---|---|---|---|---|
| CQ1 | 오페라의 유령의 출연자는? | 배우A | ✅ 배우A | ✅ ex:ActorA | ❌ 결과 없음 (추론 없음) |
| CQ2 | 샤롯데씨어터에서 열리는 뮤지컬은? | 오페라의 유령 | ⏳ | ✅ ex:PhantomOfTheOpera, "오페라의 유령" | ✅ ex:PhantomOfTheOpera, "오페라의 유령" |
| CQ3 | 5만 원 이하 티켓이 있는 공연은? | 오페라의 유령 (티켓1, 45,000원) | ⏳ | ⏳ | ✅ ex:PhantomOfTheOpera, 45000 |

- 일관성: HermiT 추론기 실행 결과 모순 없음
- CQ1은 DL Query와 Snap SPARQL 모두 **입력하지 않은 performsIn**으로 물었는데 답이 나왔습니다. hasPerformer의 역관계를 추론기가 채운 결과입니다.
- 배포본 검증: GitHub Pages에 올라간 파일(`https://johyeongseob.github.io/knowledge_graph/textbook/performance-ontology/protege_ontology.owl`)을 브라우저용 SPARQL 엔진 [Comunica](https://query.comunica.dev/)로 직접 질의했습니다. Comunica에는 추론기가 없어 **저장된 트리플만** 보므로, 추론에 기대는 CQ1은 답이 없고 저장된 사실만 쓰는 CQ2·CQ3은 답이 나옵니다.

---

## 5. 질의

### 5-1. DL Query (Protégé의 DL Query 탭)

오른쪽 Query for에서 **Instances**를 체크하고 실행합니다.

| CQ | DL Query 식 |
|---|---|
| CQ1 | `Artist and performsIn value '오페라의 유령'` |
| CQ2 | `Musical and takesPlaceAt value 샤롯데씨어터` |
| CQ3 | `Performance and inverse (ticketFor) some (Ticket and hasPrice some xsd:decimal[<= 50000])` |

### 5-2. SPARQL (Snap SPARQL)

질의는 **한 번에 하나만** 실행합니다. 기존 요소들의 ID가 WebProtégé의 무작위 문자열이라 주소를 그대로 씁니다.

| 주소 | 이름 |
|---|---|
| `wp:RClBMOPaBShii6OzewDhPEJ` | hasPerformer |
| `wp:RBB0eHzgSPRwpTD2BS6Bium` | takesPlaceAt |
| `wp:R7rThThQmUrXvESv38Kg0Gv` | hasPrice |
| `wp:RCsd3diJIePADybfDz3m44E` | hasTitle |
| `wp:R7tKT0WRhsR1fgP3MpWJsOX` | Musical |
| `ex:performsIn`, `ex:ticketFor`, `ex:개체이름` | 직접 만든 요소 |

**CQ1. 오페라의 유령의 출연자는?**
```sparql
PREFIX ex: <urn:webprotege:ontology:0529e64b-ea02-48c8-8cbc-bd2d8f15c2e6#>

SELECT ?artist WHERE {
  ?artist ex:performsIn ex:PhantomOfTheOpera .
}
```

**CQ2. 샤롯데씨어터에서 열리는 뮤지컬은?**
```sparql
PREFIX ex: <urn:webprotege:ontology:0529e64b-ea02-48c8-8cbc-bd2d8f15c2e6#>
PREFIX wp: <http://webprotege.stanford.edu/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?show ?title WHERE {
  ?show rdf:type wp:R7tKT0WRhsR1fgP3MpWJsOX .
  ?show wp:RBB0eHzgSPRwpTD2BS6Bium ex:CharlotteTheater .
  ?show wp:RCsd3diJIePADybfDz3m44E ?title .
}
```

**CQ3. 5만 원 이하 티켓이 있는 공연은?**
```sparql
PREFIX ex: <urn:webprotege:ontology:0529e64b-ea02-48c8-8cbc-bd2d8f15c2e6#>
PREFIX wp: <http://webprotege.stanford.edu/>

SELECT ?show ?price WHERE {
  ?ticket ex:ticketFor ?show .
  ?ticket wp:R7rThThQmUrXvESv38Kg0Gv ?price .
  FILTER (?price <= 50000)
}
```
