# Rust 소프트웨어 구조 설계

[English](README.md)

Rust 명령줄 도구, 서비스, 라이브러리에서 모듈과 crate 경계를 정합니다. 한 동작을 따라가며 자원을 누가 소유하고, 누가 쓰기를 수행하며, 오류는 어디서 처리할지 설계합니다.

```text
$rust-software-architecture CSV에서 유효한 행을 SQLite에 저장하고 거절된 행을 알려주는 CLI를 설계해줘. DB 연결의 소유자를 정하고, 별도 crate로 나눌 이유가 있는지 설명해줘.
```

## 부분 실패와 재실행 후 DB 결과

입력에는 서로 다른 ID를 가진 정상 행 A·B와 형식 오류 행 X가 있다. 이 흐름은 단일 crate의 내부 `parse`, `store`, `cli` 모듈로 충분하다. CLI가 SQLite 연결과 트랜잭션 수명을 소유한다. `parse`는 X의 행 번호와 거절 사유를 보고하고, `store`는 A·B를 한 트랜잭션에 쓴다. ID에는 고유 제약을 두고 재실행 때 기존 ID와 신규 삽입을 구별한다. CLI는 행 검증 오류와 저장 오류를 별도로 표현한다. 정상 행 커밋과 거절 행 요약을 마치면 종료 상태는 0이다.

| 같은 입력의 실행 순서 | 신규 행 | 실행 후 전체 행 | 보고 내용 | 종료 상태 |
|---|---:|---:|---|---:|
| B를 쓰는 시점에 I/O 실패 주입 | 0 | 0 | X와 사유 보고, B 저장 오류, 트랜잭션 롤백 | 1 |
| 실패 원인을 해소하고 재실행 | 2 (A·B) | 2 | X와 사유 보고, A·B 신규 삽입 | 0 |
| 다시 실행 | 0 | 2 | X와 사유 보고, A·B는 기존 ID | 0 |

B에서 실패하면 먼저 시도한 A의 쓰기도 롤백한다. 공개 CLI 통합 테스트에서 임시 SQLite DB로 이 세 실행을 수행하고 B 쓰기 실패를 주입해 종료 상태, 출력 구분, 매 실행 뒤 정확한 ID와 행 수를 조회한다. 파싱 단위 테스트는 X의 거절 사유를 확인한다. 독립 API나 빌드 경계가 필요해질 때 별도 crate를 검토한다.

## 설치

[스킬 ZIP 다운로드](https://github.com/Ddrubok/rust-software-architecture/releases/latest/download/rust-software-architecture.zip) 후 Codex 스킬 폴더에 압축을 풉니다. 기본 위치는 `~/.codex/skills`이고, `CODEX_HOME`을 설정했다면 `$CODEX_HOME/skills`입니다. 압축 파일 안에 이미 `rust-software-architecture` 폴더가 들어 있습니다. 설치 후 `rust-software-architecture/SKILL.md`에서 지침을 확인할 수 있습니다.

이 스킬은 단독으로 쓸 수 있습니다. 범용 스킬을 사용 중이라면 설계 도중 필요한 시점에 이 스킬을 받습니다. [범용 소프트웨어 구조 설계](https://github.com/Ddrubok/software-architecture)
