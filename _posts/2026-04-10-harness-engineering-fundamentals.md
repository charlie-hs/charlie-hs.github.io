---
layout: single
title: "Harness Engineering: LLM을 제어하는 기술"
date: 2026-04-10
categories: AI-Engineering
tags: [LLM, Harness, Prompt-Engineering, Safety]
published: true
---

## Harness Engineering이란?

**Harness**는 말 그대로 "마구(馬具)"다. 말을 제어하고 원하는 방향으로 이끌기 위한 장치. LLM에서의 Harness Engineering은 마찬가지로 **LLM의 출력을 원하는 형태로 제어하고 구조화하는 기술**을 의미한다.

LLM은 강력하지만 예측 불가능하다. 같은 프롬프트에도 다른 응답을 하고, 환각(hallucination)을 일으키며, 때로는 위험한 출력을 생성한다. Harness Engineering은 이런 **야생마 같은 LLM을 길들여** 프로덕션 환경에서 안정적으로 사용할 수 있게 만드는 기술이다.

---

## Harness Engineering vs 관련 개념

| 개념 | 초점 | 예시 |
|------|------|------|
| **Prompt Engineering** | 입력 최적화 | Few-shot, Chain-of-Thought |
| **Harness Engineering** | 출력 제어 & 구조화 | Output parsing, Retry logic |
| **Guardrailing** | 안전성 & 제약 | Content filtering, PII 마스킹 |

이 셋은 상호보완적이다. Prompt Engineering이 좋은 입력을 만들고, Harness Engineering이 출력을 제어하며, Guardrailing이 안전을 보장한다.

---

## Harness Engineering의 핵심 기법

### 1. Structured Output (구조화된 출력)

LLM의 자유로운 텍스트 출력을 JSON, XML 등 파싱 가능한 형태로 강제한다.

```python
# OpenAI의 경우
response = client.chat.completions.create(
    model="gpt-4",
    messages=[...],
    response_format={"type": "json_object"}  # JSON 모드 강제
)

# Anthropic의 경우 - 프롬프트로 유도
prompt = """
다음 형식의 JSON으로만 응답하세요:
{"name": "string", "age": number, "skills": ["string"]}

사용자 정보: 김철수, 30세, Python과 Go 사용
"""
```

**왜 중요한가?**
- 후속 처리가 가능해짐 (파싱, 검증, 변환)
- 일관된 응답 형식 보장
- 에러 핸들링 용이

### 2. Output Parsing & Validation

출력을 받은 후 검증하고 파싱하는 계층.

```python
from pydantic import BaseModel, validator

class UserInfo(BaseModel):
    name: str
    age: int
    skills: list[str]

    @validator('age')
    def age_must_be_positive(cls, v):
        if v < 0 or v > 150:
            raise ValueError('Invalid age')
        return v

# LLM 출력 검증
def parse_llm_response(raw_output: str) -> UserInfo:
    try:
        data = json.loads(raw_output)
        return UserInfo(**data)
    except (json.JSONDecodeError, ValidationError) as e:
        # 재시도 로직 또는 폴백
        raise LLMOutputError(f"Invalid output: {e}")
```

### 3. Retry & Fallback 전략

LLM은 실패한다. 항상. 이에 대비해야 한다.

```python
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=2, max=10)
)
def call_llm_with_retry(prompt: str) -> str:
    response = llm.invoke(prompt)

    # 출력 검증 - 실패시 재시도
    if not validate_output(response):
        raise InvalidOutputError("Output validation failed")

    return response

# Fallback 전략
def call_with_fallback(prompt: str) -> str:
    try:
        return call_primary_llm(prompt)  # GPT-4
    except Exception:
        return call_fallback_llm(prompt)  # Claude or local model
```

### 4. Prompt Chaining (프롬프트 체이닝)

복잡한 작업을 여러 단계로 나눠 각 단계의 출력을 제어한다.

```python
# Step 1: 의도 분류
intent = classify_intent(user_input)  # "질문" | "요청" | "불만"

# Step 2: 의도별 처리
if intent == "질문":
    # 질문 전용 프롬프트로 처리
    answer = answer_question(user_input)
elif intent == "요청":
    # 요청 처리 파이프라인
    action = parse_action(user_input)
    result = execute_action(action)
    answer = format_result(result)
else:
    # 불만 처리
    answer = handle_complaint(user_input)
```

### 5. Token & Context Management

토큰 한도 내에서 효율적으로 컨텍스트를 관리한다.

```python
def manage_context(messages: list, max_tokens: int = 4000) -> list:
    """컨텍스트 윈도우 관리"""
    total_tokens = count_tokens(messages)

    if total_tokens <= max_tokens:
        return messages

    # 전략 1: 오래된 메시지 제거 (시스템 메시지 유지)
    system_msg = messages[0]
    recent_msgs = messages[1:]

    while count_tokens([system_msg] + recent_msgs) > max_tokens:
        recent_msgs.pop(0)  # 가장 오래된 메시지 제거

    return [system_msg] + recent_msgs
```

---

## 실전 Harness 패턴

### Pattern 1: Extract-Transform-Validate

```
[Raw LLM Output] → [Extract] → [Transform] → [Validate] → [Use]
```

```python
class LLMHarness:
    def __init__(self, llm, schema: BaseModel):
        self.llm = llm
        self.schema = schema

    def invoke(self, prompt: str) -> BaseModel:
        # 1. LLM 호출
        raw_output = self.llm.invoke(prompt)

        # 2. Extract: JSON 추출
        json_str = self._extract_json(raw_output)

        # 3. Transform: 필요시 변환
        data = json.loads(json_str)
        data = self._transform(data)

        # 4. Validate: 스키마 검증
        return self.schema(**data)

    def _extract_json(self, text: str) -> str:
        """텍스트에서 JSON 부분만 추출"""
        # ```json ... ``` 블록 찾기
        match = re.search(r'```json\s*(.*?)\s*```', text, re.DOTALL)
        if match:
            return match.group(1)
        # 또는 { } 블록 찾기
        match = re.search(r'\{.*\}', text, re.DOTALL)
        if match:
            return match.group(0)
        raise ExtractionError("No JSON found in output")
```

### Pattern 2: Multi-Model Consensus

중요한 결정은 여러 모델의 합의를 거친다.

```python
async def consensus_call(prompt: str, threshold: float = 0.66) -> str:
    """여러 모델의 합의된 응답 반환"""
    models = [gpt4, claude, gemini]

    # 병렬 호출
    responses = await asyncio.gather(*[
        m.ainvoke(prompt) for m in models
    ])

    # 응답 클러스터링 및 합의 도출
    consensus = find_consensus(responses, threshold)

    if consensus is None:
        raise NoConsensusError("Models disagree")

    return consensus
```

---

## LangChain에서의 Harness Engineering

LangChain은 이런 패턴들을 추상화해서 제공한다.

```python
from langchain.output_parsers import PydanticOutputParser
from langchain.prompts import PromptTemplate

# 1. 출력 파서 정의
parser = PydanticOutputParser(pydantic_object=UserInfo)

# 2. 프롬프트에 포맷 지시사항 주입
prompt = PromptTemplate(
    template="사용자 정보를 추출하세요.\n{format_instructions}\n\n입력: {input}",
    input_variables=["input"],
    partial_variables={"format_instructions": parser.get_format_instructions()}
)

# 3. 체인 구성
chain = prompt | llm | parser  # 자동으로 파싱 & 검증
```

---

## 정리

Harness Engineering은 **LLM을 프로덕션에서 쓸 수 있게 만드는 실용 기술**이다.

핵심 원칙:
1. **신뢰하지 말고 검증하라** - LLM 출력은 항상 검증
2. **실패를 예상하라** - Retry, Fallback 필수
3. **구조화하라** - 자유 텍스트보다 파싱 가능한 형식
4. **단계를 나눠라** - 복잡한 작업은 체이닝으로

---

## 다음 학습

- [[Guardrailing 기초]]: 안전한 LLM 출력을 위한 필터링
- [[LangChain 입문]]: Harness 패턴의 실전 적용
- [[Prompt Engineering 심화]]: 더 좋은 입력 만들기
