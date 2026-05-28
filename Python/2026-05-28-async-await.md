# async / await

## 왜 필요한가

일반 함수는 I/O 작업(DB 조회, API 호출) 중에 CPU가 멈춰서 기다림.
async 함수는 기다리는 동안 다른 요청을 처리할 수 있음.

## 핵심 개념

```python
# 동기 - 순서대로 블로킹
def get_user():
    result = db.query(...)  # 여기서 멈춤
    return result

# 비동기 - I/O 중에 다른 작업 가능
async def get_user():
    result = await db.execute(...)  # 기다리는 동안 다른 요청 처리
    return result
```

## await를 붙이는 기준

- DB 쿼리, 외부 API 호출, 파일 읽기 등 I/O 작업에만 붙임
- CPU 연산(반복문, 계산)에는 안 붙임

## FastAPI에서 사용

```python
@router.get("/users/{id}")
async def get_user(id: int, db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(User).where(User.id == id))
    return result.scalar_one()
```

## 주의사항

- `async def` 안에서 일반 동기 함수를 `await` 없이 호출하면 블로킹 발생
- SQLAlchemy 2.0은 `AsyncSession` 사용해야 async 제대로 동작
