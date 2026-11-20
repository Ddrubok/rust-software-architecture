# Rust 소프트웨어 구조 설계

[English](README.md)

Rust 명령줄 도구, 서비스, 라이브러리에서 모듈과 crate 경계를 정합니다. 한 동작을 따라가며 자원을 누가 소유하고, 누가 쓰기를 수행하며, 오류는 어디서 처리할지 설계합니다.

```text
$rust-software-architecture CSV에서 유효한 행을 SQLite에 저장하고 거절된 행을 알려주는 CLI를 설계해줘. DB 연결의 소유자를 정하고, 별도 crate로 나눌 이유가 있는지 설명해줘.
```

## 설치

[스킬 ZIP 다운로드](https://github.com/Ddrubok/rust-software-architecture/releases/latest/download/rust-software-architecture.zip) 후 Codex 스킬 폴더에 압축을 풉니다. 기본 위치는 `~/.codex/skills`이고, `CODEX_HOME`을 설정했다면 `$CODEX_HOME/skills`입니다. 압축 파일 안에 이미 `rust-software-architecture` 폴더가 들어 있습니다. 설치 후 `rust-software-architecture/SKILL.md`에서 지침을 확인할 수 있습니다.

이 스킬은 단독으로 쓸 수 있습니다. 범용 스킬을 사용 중이라면 설계 도중 필요한 시점에 이 스킬을 받습니다. [범용 소프트웨어 구조 설계](https://github.com/Ddrubok/software-architecture)
