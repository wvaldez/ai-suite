---
name: build-ref
description: Build and test commands by ecosystem (.NET, Node, Java, Python, Go, Rust). Load when you need to run compilation checks or test suites and the project's commands are not already known from context.
---

## Compilation / build check

| Ecosystem | Command |
|---|---|
| .NET / C# | `dotnet build` |
| Node / TypeScript | `tsc --noEmit` or `npm run build` |
| Java / Maven | `mvn compile -q` |
| Java / Gradle | `./gradlew compileJava` |
| Python | `python -m py_compile **/*.py` |
| Go | `go build ./...` |
| Rust | `cargo check` |

If the build tool is not in this table, ask the user: "What command builds this project?"

---

## Test runner

| Ecosystem | Command |
|---|---|
| .NET | `dotnet test` |
| Node / Jest | `npm test` or `npx jest` |
| Node / Vitest | `npx vitest run` |
| Java / Maven | `mvn test -q` |
| Java / Gradle | `./gradlew test` |
| Python / pytest | `pytest` |
| Go | `go test ./...` |
| Rust | `cargo test` |

If the test command is not in this table, ask the user.
