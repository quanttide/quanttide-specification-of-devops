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

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `dir` | `string` | — | **必填**。子目录路径（相对于仓库根目录） |
| `language` | `string` | `"auto"`（自动检测） | 编程语言 |
| `framework` | `string` | `""` | 框架名称 |
| `build_tool` | `string` | `"auto"`（自动检测） | 构建工具 |
| `registry` | `string` | 继承全局 `platform.artifact_registry` | 制品库 |
| `release` | `object` | 继承全局 `stages.release` | 发布配置 |
| `test_threshold` | `float` 或 `null` | 继承全局 `stages.test.threshold` | 测试阈值 |
| `ci_workflow` | `string` 或 `null` | 按 `build-{scope}` 约定 | CI workflow 名称 |

### language 枚举

| YAML 值 | Rust 变体 | 说明 |
|---------|-----------|------|
| `rust` | `Rust` | Rust |
| `python` | `Python` | Python |
| `go` | `Go` | Go |
| `dart` | `Dart` | Dart |
| `typescript` / `ts` / `node` | `TypeScript` | TypeScript |
| (其他) | `Unknown(s)` | 未知语言，不崩溃 |

### build_tool 枚举

| YAML 值 | Rust 变体 | 说明 |
|---------|-----------|------|
| `cargo` | `Cargo` | Cargo（Rust） |
| `uv` / `poetry` / `pdm` | `Uv` | Python 构建工具 |
| `go` | `Go` | Go |
| `flutter` | `Flutter` | Flutter（Dart） |
| `npm` / `pnpm` / `yarn` / `bun` | `Npm` | Node 包管理器 |
| (其他) | `Unknown(s)` | 未知工具，不崩溃 |

### 覆盖语义

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
