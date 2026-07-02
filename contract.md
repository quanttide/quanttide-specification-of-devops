# 契约文件规范

`.quanttide/devops/contract.yaml` 定义项目的四维治理契约。由 `quanttide-devops` toolkit 解析加载。

## 整体结构

```yaml
stages:       # 时序维度 — 生命周期各阶段的配置
  build:      #   构建阶段（command 可选）
  test:       #   测试阶段（command + 覆盖率阈值）
  release:    #   发布阶段（CHANGELOG 路径 + 发布前脚本）

platform:     # 载体维度 — 外部治理载体
  source_control:     # 源代码管理平台
  pipeline:           # CI/CD 平台
  artifact_registry:  # 默认制品库

sources:      # 事实源维度 — 真相中心
  version:    #   版本号从哪里读
    type:     #     来源类型
    path:     #     自定义路径

scopes:       # 上下文维度 — 规则边界
  <name>:     #   作用域名（即 tag 前缀）
    dir:      #     子目录路径
    language:       #     编程语言
    framework:      #     框架
    build_tool:     #     构建工具
    registry:       #     制品库（覆盖全局 platform.artifact_registry）
    release:        #     发布配置（覆盖全局 stages.release）
    test_threshold: #     测试阈值（覆盖全局 stages.test.threshold）
    ci_workflow:    #     CI workflow 名称（覆盖 build-{scope} 约定）
```

所有维度均有默认值，scope 只需声明差异部分。

## 字段说明

### stages — 时序维度

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `build.command` | `string` 或 `null` | `null` | 构建命令 |
| `test.command` | `string` 或 `null` | `null` | 测试命令 |
| `test.threshold` | `float` | `70.0` | 覆盖率阈值（百分比） |
| `release.changelog` | `string` | `"CHANGELOG.md"` | CHANGELOG 文件路径 |
| `release.pre_publish` | `string[]` | `[]` | 发布前执行的脚本列表 |

### platform — 载体维度

| 字段 | 可选值 | 默认值 | 说明 |
|------|--------|--------|------|
| `source_control` | `github`, `gitlab`, `gitee` | `github` | 源代码管理平台 |
| `pipeline` | `github_actions`, `gitlab_ci`, `jenkins` | `github_actions` | CI/CD 平台 |
| `artifact_registry` | `crates`, `pypi`, `pubdev`, `npm`, `github_releases`, `docker`, `none` | `none` | 制品库类型 |

### sources — 事实源维度

| 字段 | 可选值 | 默认值 | 说明 |
|------|--------|--------|------|
| `version.type` | `cargo`, `pyproject`, `tag`, `pubspec`, `package.json`, `auto` | `auto` | 版本号来源类型 |
| `version.path` | `string` 或 `null` | `null` | 自定义配置文件路径 |

`type` 的检测策略（`auto` 时按优先级自动选择）：`Cargo.toml` > `pyproject.toml` > `pubspec.yaml` > `package.json`。无匹配时使用 `tag`（只从 git tag 读）。

### scopes — 上下文维度

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `dir` | `string` | — | **必填**。子目录路径（相对于仓库根目录） |
| `language` | `string` | `"auto"`（自动检测） | 编程语言 |
| `framework` | `string` | `""` | 框架名称 |
| `build_tool` | `string` | `"auto"`（自动检测） | 构建工具 |
| `registry` | `string` | 继承全局 | 制品库（覆盖 `platform.artifact_registry`） |
| `release` | `object` | 继承全局 | 发布配置（覆盖 `stages.release`） |
| `test_threshold` | `float` 或 `null` | 继承全局 | 测试阈值（覆盖 `stages.test.threshold`） |
| `ci_workflow` | `string` 或 `null` | 按 `build-{scope}` 约定 | CI workflow 名称 |

#### language 可选值

`rust`, `python`, `go`, `dart`, `typescript`（或别名 `ts`、`node`）。未知值视为 `Unknown`，不崩溃。

#### build_tool 可选值

`cargo`, `uv`（或别名 `poetry`、`pdm`）, `go`, `flutter`, `npm`（或别名 `pnpm`、`yarn`、`bun`）。未知值视为 `Unknown`，不崩溃。

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

## 参考实现

- 解析器：`quanttide-devops` crate — `contract::load()` / `contract::load_from_str()`
- 源码：`packages/toolkit/packages/rust/src/contract/`
- 理论：`docs/essay/contract/index.md`
