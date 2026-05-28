# SQLAlchemy pool_recycle

## 문제 상황

MySQL은 기본적으로 일정 시간(wait_timeout, 기본 8시간) 동안 쿼리가 없으면 연결을 끊음.
SQLAlchemy는 커넥션 풀에 연결을 재사용하는데, MySQL이 끊은 걸 모르고 재사용하면 에러 발생.

```
OperationalError: (2006, 'MySQL server has gone away')
```

## 해결

```python
engine = create_engine(
    DATABASE_URL,
    pool_recycle=1800,  # 30분마다 연결 갱신
    pool_pre_ping=True  # 쿼리 전 연결 살아있는지 확인
)
```

## pool_pre_ping vs pool_recycle 차이

| | pool_pre_ping | pool_recycle |
|---|---|---|
| 동작 | 쿼리 전마다 ping 테스트 | 일정 시간마다 연결 교체 |
| 오버헤드 | 매 요청마다 ping 비용 | 없음 |
| 추천 상황 | 간헐적 연결 끊김 | 장시간 운영 서버 |

## 실제 적용 (WorkB 프로젝트에서 트러블슈팅)

n8n에서 FastAPI로 전환 후 장시간 미사용 시 DB 연결 오류 발생.
`pool_recycle=1800` 설정으로 해결.
