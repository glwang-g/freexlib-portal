# freexlib-portal

Freexlib 门户与跨项目契约仓库。这个仓库刻意保持很小：只维护两样东西。

## 内容

- `index.html` — 门户首页，部署到 <https://freexlib.com>
- `docs/world-contract.md` / `docs/world-contract.zh-CN.md` — 三个空间共享的世界契约（词汇表、事件形态、分层映射）
- `.github/workflows/deploy.yml` — 推送 `master` 后把门户安装到服务器
- `deploy/nginx/freexlib-portal-static.conf` — 服务器 nginx 片段

## 三个空间

| 空间 | 地址 | 定位 |
| --- | --- | --- |
| Swarm Space | <https://swarm.freexlib.com> | Battlecode 风格的群智对抗，Rust 权威引擎样板 |
| XShow Labs | <https://labs.freexlib.com> | 从信号到算法的计算机科学教育主线 |
| Living World | <https://living.freexlib.com> | 可进入、可修改、可回看的持久方块世界 |
| App Forge | <https://app.freexlib.com> | 聊天生成、验证并发布可安装小应用 |

## 世界契约

新增规则、动作、事件或回放概念之前，先读 `docs/world-contract.zh-CN.md`。
共享术语的变更需要同步更新中英文两个版本。

## 部署

推送 `master` 触发 GitHub Actions（GitHub-hosted runner），通过 SSH 把 `index.html`
推送到服务器 `/var/www/freexlib-portal` 并重载 nginx。首次搭建：

1. 创建 GitHub 仓库（建议 `glwang-g/freexlib-portal`）并推送本目录；
2. 在仓库 `Settings → Secrets and variables → Actions → Secrets` 配置
   `DEPLOY_HOST`、`DEPLOY_USER`、`DEPLOY_PORT`、`DEPLOY_PATH`、
   `DEPLOY_SSH_KEY` 和 `DEPLOY_KNOWN_HOSTS`；
3. 确保部署用户可以无密码执行 `sudo install`、`sudo nginx -t` 和
   `sudo systemctl reload nginx`；
4. 确认服务器 nginx 把 `freexlib.com` 指向 `/var/www/freexlib-portal`
   （片段见 `deploy/nginx/`）。

## 本地预览

```bash
python3 -m http.server 8080
```

然后打开 <http://localhost:8080>。
