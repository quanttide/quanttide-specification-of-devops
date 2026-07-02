# Stage 配置

时序维度，定义价值流的节拍。不规定"怎么做"，只规定"什么时候检查什么"。

## 结构

```yaml
stages:
  build:       # 构建阶段
  test:        # 测试阶段
  release:     # 发布阶段
```

## 字段

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `build.command` | `string` 或 `null` | `null` | 构建命令 |
| `test.command` | `string` 或 `null` | `null` | 测试命令 |
| `test.threshold` | `float` | `70.0` | 覆盖率阈值（百分比） |
| `release.changelog` | `string` | `"CHANGELOG.md"` | CHANGELOG 文件路径 |
| `release.pre_publish` | `string[]` | `[]` | 发布前执行的脚本列表 |

## 示例

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
```
