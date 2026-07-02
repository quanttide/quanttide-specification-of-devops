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

| 字段 | 可选值 | 默认值 | 说明 |
|------|--------|--------|------|
| `source_control` | `github`, `gitlab`, `gitee` | `github` | 源代码管理平台 |
| `pipeline` | `github_actions`, `gitlab_ci`, `jenkins` | `github_actions` | CI/CD 平台 |
| `artifact_registry` | `crates`, `pypi`, `pubdev`, `npm`, `github_releases`, `docker`, `none` | `none` | 制品库类型 |

### source_control 枚举

| YAML 值 | Rust 变体 | 说明 |
|---------|-----------|------|
| `github` | `Github` | GitHub |
| `gitlab` | `Gitlab` | GitLab |
| `gitee` | `Gitee` | Gitee |

### pipeline 枚举

| YAML 值 | Rust 变体 | 说明 |
|---------|-----------|------|
| `github_actions` | `GithubActions` | GitHub Actions |
| `gitlab_ci` | `GitlabCi` | GitLab CI |
| `jenkins` | `Jenkins` | Jenkins |

### artifact_registry 枚举

| YAML 值 | Rust 变体 | 显示名 | 说明 |
|---------|-----------|--------|------|
| `crates` | `Crates` | crates.io | Rust 包注册源 |
| `pypi` | `PyPI` | PyPI | Python 包注册源 |
| `pubdev` | `PubDev` | pub.dev | Dart/Flutter 包注册源 |
| `npm` | `Npm` | npm | Node/TypeScript 包注册源 |
| `github_releases` | `GitHubReleases` | GitHub Releases | GitHub 发布 |
| `docker` | `Docker` | Docker | 容器镜像仓库 |
| (其他) | `None` | (none) | 无制品库 |

## 示例

```yaml
platform:
  source_control: github
  pipeline: github_actions
  artifact_registry: crates
```
