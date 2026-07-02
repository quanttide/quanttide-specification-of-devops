# Source 配置

事实源维度，定义真相中心——版本号从哪读、格式是什么。无论换到哪个 Platform，Sources 里的真理永远不变。

## 结构

```yaml
sources:
  version:
    type:       # 来源类型（必选）
    path:       # 自定义路径（可选）
```

## 字段

| 字段 | 可选值 | 默认值 | 说明 |
|------|--------|--------|------|
| `version.type` | `cargo`, `pyproject`, `tag`, `pubspec`, `package.json`, `auto` | `auto` | 版本号来源类型 |
| `version.path` | `string` 或 `null` | `null` | 自定义配置文件路径 |

### version.type 枚举

| YAML 值 | Rust 变体 | 对应文件 | 说明 |
|---------|-----------|---------|------|
| `cargo` | `Cargo` | `Cargo.toml` | Rust 项目 |
| `pyproject` | `Pyproject` | `pyproject.toml` | Python 项目（PEP 621） |
| `tag` | `TagOnly` | 无 | 不从配置文件读版本，只从 git tag 读 |
| `pubspec` | `Pubspec` | `pubspec.yaml` | Dart/Flutter 项目 |
| `package.json` | `PackageJson` | `package.json` | Node/TypeScript 项目 |
| `auto` | `Auto` | 自动检测 | 按优先级自动选择 |

### 自动检测策略

`auto` 时按优先级检测：`Cargo.toml` > `pyproject.toml` > `pubspec.yaml` > `package.json`。无匹配时使用 `tag`（只从 git tag 读）。

## 示例

```yaml
sources:
  version:
    type: cargo
    path: Cargo.toml
```
