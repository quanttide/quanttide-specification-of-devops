# 契约文件规范

`.quanttide/devops/contract.yaml` 定义项目的四维治理契约。由 `quanttide-devops` toolkit 解析加载。

## 四维架构

```yaml
stages:      # 时序维度 — 何时检查什么
platform:    # 载体维度 — 外部治理平台
sources:     # 事实源维度 — 真相的本质
scopes:      # 上下文维度 — 规则的作用范围
```

各维度详细配置见：

- [Stage 配置](stage.md) — 构建/测试/发布阶段
- [Platform 配置](platform.md) — 源代码管理/CI/制品库
- [Source 配置](source.md) — 版本号来源
- [Scope 配置](scope.md) — 作用域定义与覆盖

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

## 最小示例

只定义 scope，其余用默认值：

```yaml
scopes:
  cli:
    dir: src/cli
```

`language`、`build_tool` 等字段自动检测。所有维度使用默认值。

## 覆盖语义（浅覆盖）

Scope 级有值就用 scope 的，没有就用全局的。不是深度合并。

```yaml
stages:
  test:
    threshold: 70

scopes:
  cli:
    dir: src/cli
    # test_threshold 未设置 → 使用全局 70
  sensitive:
    dir: src/sensitive
    test_threshold: 95  # 覆盖全局
```

## 向下兼容

旧格式 `scopes: { name: dir }`（简单键值对）已不再支持。所有 `contract.yaml` 必须使用新格式（scope 值为对象）。旧格式迁移：

```yaml
# 旧格式（不再支持）
scopes:
  cli: src/cli

# 新格式
scopes:
  cli:
    dir: src/cli
```
