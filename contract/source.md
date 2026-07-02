# Source 配置

事实源维度，定义真相中心——版本号从哪读、格式是什么。无论换到哪个 Platform，Sources 里的真理永远不变。

## 结构

```yaml
sources:
  version:
    type:       # 来源类型
    path:       # 自定义路径（可选）
```

## 字段

- `version.type` — 默认值：`auto`。版本号来源类型。
  - `cargo` — 从 `Cargo.toml` 的 `version` 字段读取（Rust 项目）
  - `pyproject` — 从 `pyproject.toml` 的 `version` 字段读取（Python 项目，PEP 621）
  - `tag` — 不从配置文件读版本，只从 git tag 读取
  - `pubspec` — 从 `pubspec.yaml` 的 `version` 字段读取（Dart/Flutter 项目）
  - `package.json` — 从 `package.json` 的 `version` 字段读取（Node/TypeScript 项目）
  - `auto` — 自动检测。按优先级检测：`Cargo.toml` > `pyproject.toml` > `pubspec.yaml` > `package.json`。无匹配时使用 `tag`。
- `version.path` — 类型：`string` 或 `null`，默认值：`null`。自定义配置文件路径。

## 示例

```yaml
sources:
  version:
    type: cargo
    path: Cargo.toml
```
