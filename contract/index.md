# 契约文件规范

`.quanttide/devops/contract.yaml` 定义项目的四维治理契约。由 `quanttide-devops` toolkit 解析加载。

## 四维架构

```yaml
stages:      # 时序维度 — 何时检查什么
platform:    # 载体维度 — 外部治理平台
sources:     # 事实源维度 — 真相的本质
scopes:      # 上下文维度 — 规则的作用范围
```

## 完整示例

```yaml
stages:
  build:
    command: cargo build --release
  test:
    command: cargo test
    threshold: 80
  release:
    changelog: CHANGELOG.md
    pre_publish:
      - scripts/preflight.sh

platform:
  source_control: github
  pipeline: github_actions
  artifact_registry: crates

sources:
  version:
    type: cargo
    path: Cargo.toml

scopes:
  cli:
    dir: src/cli
    language: rust
    build_tool: cargo
    registry: crates
  studio:
    dir: src/studio
    language: dart
    build_tool: flutter
    registry: pubdev
    release:
      changelog: src/studio/CHANGELOG.md
    ci_workflow: studio-pipeline
```


