# Platform 配置

载体维度，定义外部治理载体。负责"外部合规"——平台提供舞台并制定入场规则。

## 结构

```yaml
platform:
  source_control:      # 源代码管理平台
  pipeline:            # CI/CD 平台
  artifact_registry:   # 制品库
```

## 字段

- `source_control` — 默认值：`github`。源代码管理平台。
  - `github` — GitHub
  - `gitlab` — GitLab
  - `gitee` — Gitee
- `pipeline` — 默认值：`github_actions`。CI/CD 平台。
  - `github_actions` — GitHub Actions
  - `gitlab_ci` — GitLab CI
  - `jenkins` — Jenkins
- `artifact_registry` — 默认值：`none`。制品库类型。
  - `crates` — crates.io（Rust 包注册源）
  - `pypi` — PyPI（Python 包注册源）
  - `pubdev` — pub.dev（Dart/Flutter 包注册源）
  - `npm` — npm（Node/TypeScript 包注册源）
  - `github_releases` — GitHub Releases
  - `docker` — Docker（容器镜像仓库）
  - 其他值或未设置 — `none`（无制品库）

## 示例

```yaml
platform:
  source_control: github
  pipeline: github_actions
  artifact_registry: crates
```
