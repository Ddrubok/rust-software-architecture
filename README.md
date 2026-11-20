# Rust Software Architecture

[한국어](README.ko.md)

Choose module and crate boundaries for a Rust command, service, or library. Trace who owns a resource, who can write to it, and who handles a failure.

```text
$rust-software-architecture Design a CLI that reads a CSV file, stores valid rows in SQLite, and reports rejected rows. Who owns the connection? Would a second crate serve a purpose?
```

## CSV to SQLite with partial failure

Input has valid rows A and B with distinct IDs, plus malformed row X. One crate with private `parse`, `store`, and `cli` modules is enough for this flow. The CLI opens and owns the SQLite connection and transaction lifetime; `parse` reports X with its row number and reason, and `store` writes A and B in one transaction. IDs have a uniqueness constraint, and reruns distinguish existing IDs from new inserts. The CLI prints parse rejections separately from storage failures. A rejected row does not fail the command when all valid rows commit and the rejection summary is printed.

| Run with the same input | New rows | Total rows after run | Report | Exit |
|---|---:|---:|---|---:|
| Inject I/O failure while writing B | 0 | 0 | X rejected with reason; storage error on B; transaction rolled back | 1 |
| Remove failure and rerun | 2 (A, B) | 2 | X rejected with reason; A and B inserted | 0 |
| Rerun once more | 0 | 2 | X rejected with reason; A and B already existed | 0 |

The rollback includes A, even though its insert was attempted before B failed. A public CLI integration test runs those three invocations against a temporary SQLite database, injects the B write failure, and checks exit statuses, output categories, and exact ID/count queries after each run. A parsing unit test checks X's reported reason. A separate crate needs an independent API or build boundary; this input alone does not require one.

## Install

[Download the skill ZIP](https://github.com/Ddrubok/rust-software-architecture/releases/latest/download/rust-software-architecture.zip) and extract it into your Codex skills directory, `$CODEX_HOME/skills` or the default `~/.codex/skills`. The ZIP contains the `rust-software-architecture` folder. After extraction, open `rust-software-architecture/SKILL.md` to read the instructions.

You can use this skill on its own. If you already use the general skill, it can fetch this specialist during a design task. [Software Architecture](https://github.com/Ddrubok/software-architecture)
