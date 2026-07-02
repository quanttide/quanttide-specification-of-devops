# Scope 配置

上下文维度，为不同组件挂载不同的 Stages、Platforms、Sources 组合，避免"一刀切"。

## 结构

```yaml
scopes:
  <name>:
    dir:               # 子目录路径（必填）
    language:          # 编程语言
    framework:         # 框架
    build_tool:        # 构建工具
    registry:          # 制品库（覆盖全局 platform.artifact_registry）
    release:           # 发布配置（覆盖全局 stages.release）
    test_threshold:    # 测试阈值（覆盖全局 stages.test.threshold）
    ci_workflow:       # CI workflow 名称
```

## 字段

- `dir` — 类型：`string`，必填。子目录路径（相对于仓库根目录）。
- `language` — 默认值：自动检测（`auto`）。编程语言。
  - `rust` — Rust
  - `python` — Python
  - `go` — Go
  - `dart` — Dart
  - `typescript` / `ts` / `node` — TypeScript
  - 其他值 — 视为 `Unknown`，不崩溃
- `framework` — 类型：`string`，默认值：`""`。框架名称。
- `build_tool` — 默认值：自动检测（`auto`）。构建工具。
  - `cargo` — Cargo（Rust）
  - `uv` / `poetry` / `pdm` — uv（Python）
  - `go` — Go
  - `flutter` — Flutter（Dart）
  - `npm` / `pnpm` / `yarn` / `bun` — npm（Node）
  - 其他值 — 视为 `Unknown`，不崩溃
- `registry` — 默认值：继承全局 `platform.artifact_registry`。制品库（可选值与 Platform 一致）。
- `release` — 类型：`object`，默认值：继承全局 `stages.release`。发布配置（可选字段与 Stage 一致）。
- `test_threshold` — 类型：`float` 或 `null`，默认值：继承全局 `stages.test.threshold`。测试阈值。
- `ci_workflow` — 类型：`string` 或 `null`，默认值：按 `build-{scope}` 约定。CI workflow 名称。

## 覆盖语义

Scope 的覆盖是**浅覆盖**：scope 级有值就用 scope 的，没有就用全局的。

```yaml
stages:
  release:
    changelog: CHANGELOG.md
    pre_publish:
      - cargo publish

scopes:
  cli:
    dir: src/cli
    # release 不写 → 继承全局 stages.release
  studio:
    dir: src/studio
    release:
      changelog: src/studio/CHANGELOG.md
    # pre_publish 不写 → 继承全局
```

## 示例

```yaml
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
