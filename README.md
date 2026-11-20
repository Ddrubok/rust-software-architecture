# Rust Software Architecture

[한국어](README.ko.md)

Choose module and crate boundaries for a Rust command, service, or library. Trace who owns a resource, who can write to it, and who handles a failure.

```text
$rust-software-architecture Design a CLI that reads a CSV file, stores valid rows in SQLite, and reports rejected rows. Who owns the connection? Would a second crate serve a purpose?
```

## Install

[Download the skill ZIP](https://github.com/Ddrubok/rust-software-architecture/releases/latest/download/rust-software-architecture.zip) and extract it into your Codex skills directory, `$CODEX_HOME/skills` or the default `~/.codex/skills`. The ZIP contains the `rust-software-architecture` folder. After extraction, open `rust-software-architecture/SKILL.md` to read the instructions.

You can use this skill on its own. If you already use the general skill, it can fetch this specialist during a design task. [Software Architecture](https://github.com/Ddrubok/software-architecture)
