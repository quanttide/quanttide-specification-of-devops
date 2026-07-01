# 契约文件规范

`.quanttide/devops/contract.yaml` 定义单仓多组件项目的作用域映射。

## 文件格式

```yaml
# 作用域（tag 前缀）到子目录的映射
scopes:
  <scope名称>: <子目录路径>
```

scope 名称即 tag 前缀。子目录路径相对于仓库根目录。支持多层目录（如 `src/cli`）。

## 示例

单作用域：

```yaml
scopes:
  cli: .
```

多作用域（monorepo）：

```yaml
scopes:
  cli: src/cli
  studio: src/studio
  provider: src/provider
```

不带 scope 前缀的 tag（如 `v0.1.0`）默认属于根作用域，无需在配置中声明。
