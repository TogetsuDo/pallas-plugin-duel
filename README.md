<p align="center">
  <img src="./assets/brand-avatar.png" width="220" height="220" alt="牛牛决斗">
</p>

<h1 align="center">牛牛决斗 pallas-plugin-duel</h1>

<p align="center">提供多幕决斗、八角笼与 QTE 抢答玩法。</p>

<p align="center">
  <img alt="官方插件" src="https://img.shields.io/badge/%E5%AE%98%E6%96%B9%E6%8F%92%E4%BB%B6-FE7D37">
  <img alt="控制台插件商店" src="https://img.shields.io/badge/%E6%8E%A7%E5%88%B6%E5%8F%B0-%E6%8F%92%E4%BB%B6%E5%95%86%E5%BA%97-4EA94B">
  <img alt="安装命令" src="https://img.shields.io/badge/uv%20run%20pallas%20ext%20install%20pallas--plugin--duel-586069">
  <img alt="PyPI 版本" src="https://img.shields.io/pypi/v/pallas-plugin-duel?label=%E7%89%88%E6%9C%AC&color=2563EB">
</p>

## 安装方式

需已安装 [Pallas-Bot](https://github.com/PallasBot/Pallas-Bot) **≥ 4.0**。

推荐直接在控制台插件商店安装，或在本体项目中执行：

```bash
uv run pallas ext install pallas-plugin-duel
```

也可单独安装本包：

```bash
uv pip install pallas-plugin-duel
```

开发联调：clone 本仓库后 `uv pip install -e .`（`pyproject.toml` 可配置本体 path 依赖）。

## 多进程分片

Pallas-Bot 支持单进程，也支持 **hub + 多个 worker** 的多进程部署。启用分片时：

- **hub 与每个 worker 须安装相同版本的本扩展包**；
- 各进程共享同一路径的 **`data/`**（注册表、协调状态、WebUI 落盘等）；
- 跨进程互斥与状态同步依赖 Redis 协调层（配置见文档站）。

本插件通过本体 **`plugin_coord`** 与启动时的 **`register_duel_coord()`** / **`register_fleet_probe()`** 接入协调层；未安装扩展时不影响 core 插件运行。

详见：[多进程分片 · 架构说明](https://PallasBot.github.io/Pallas-Bot-Docs/architecture/bot-process-sharding)

## 怎么使用

泰拉风味多幕决斗：事件包、干员/关键词 QTE、双牛八角笼、胜负惩罚。

### 用户命令

| 口令 / 触发 | 场景 | 说明 |
| --- | --- | --- |
| 牛牛决斗 @对手 [N幕\|N回合] | 群内 | 对人或单牛 |
| 牛牛决斗 @牛A @牛B | 群内 | 双牛对决 |
| 八角笼牛 [N幕\|N回合] | 群内 | 随机两只在线牛牛 |
| 按幕面提示答干员名/关键词 | 群内 | QTE 抢答 |
| 决斗事件重载 | 群内 | 热更新事件包（群管） |

### 命令权限

| 命令 ID | 默认等级 |
| --- | --- |
| `duel.duel` | everyone |
| `duel.cage` | everyone |
| `duel.reload_events` | group_moderator |

> 详细用法、限制条件和可用范围以帮助为主。

## 配置项

WebUI **插件 → duel**，或本仓库 [`config.py`](src/pallas_plugin_duel/config.py)。事件包约定见 [`event_packs/README.md`](src/pallas_plugin_duel/event_packs/README.md)。

干员资源同步（在本体仓库执行）：

```bash
uv run python scripts/fetch_arknights_duel_data.py
```

## 排障

| 现象 | 处理 |
| --- | --- |
| 无法开战 | 同群仅一场；检查 @ 与在线牛 |
| 乱入无头像 | 执行资源脚本补 `resource/arknights/avatars` |

## 实现

源码位置：[`src/pallas_plugin_duel/`](src/pallas_plugin_duel/)

实现要点：

- 决斗事件、QTE 与多牛牛在线判定共同组成一场完整对局。
- 分片模式下通过协调层维持同群互斥与事件同步。
- 干员资源与头像不全时会直接影响决斗展示效果。

## 相关链接

| 说明 | 链接 |
| --- | --- |
| 牛牛决斗 · 用户文档 | [文档站 · duel](https://PallasBot.github.io/Pallas-Bot-Docs/plugins/duel) |
| 插件开发入门 | [develop/plugin/getting-started](https://PallasBot.github.io/Pallas-Bot-Docs/develop/plugin/getting-started) |
| 多进程分片 | [architecture/bot-process-sharding](https://PallasBot.github.io/Pallas-Bot-Docs/architecture/bot-process-sharding) |
