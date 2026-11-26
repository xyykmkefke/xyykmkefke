- 状态：进行中
- 创建日期：2026-11-26
- 最后更新：2026-11-26
- 一句话说明：GitHub 主页 README 的线上产物仓库，只放最终展示内容。
- 归档原因：无

## 这个仓库是什么

本仓库就是 `xyykmkefke/xyykmkefke`，GitHub 用它作为**主页展示入口**：仓库名与账号同名且公开、根目录 `README.md` 非空，主页才会渲染这份 README。

它**只放最终产物**：`README.md` + `assets/*.svg`。生成器源码、快照、内部报告都在另一个私有仓库 `profile-readme-pipeline`，不放这里。

## 内容怎么来的

1. 在私有工具仓库跑 `python run.py sync` 拉取公开账户数据到 JSON 快照；
2. `python run.py build` 渲染 `out/README.md` 与三张 SVG，并通过全部门禁；
3. `python run.py check` 确认产物逐字节一致；
4. 把 `out/` 的产物复制到本仓库，提交并推送。

不依赖 GitHub Action、不依赖第三方图片服务、不依赖 API token。

## 人工编辑约定

`README.md` 中 `<!-- BEGIN HUMAN ... -->` 与 `<!-- END HUMAN -->` 之间是人工区，可以直接改，重新生成时会保留。标记区外的内容由生成器接管，手改会在下次 build 时被覆盖。

## 产物说明

| 文件 | 内容 |
|---|---|
| `README.md` | 主页正文：身份、当前焦点、联系方式、三张卡片、仓库链接 |
| `assets/numbers-dark.svg` / `-light.svg` | 公开仓库数 / 关注者 / 获星 |
| `assets/repositories-dark.svg` / `-light.svg` | 精选仓库与简介 |
| `assets/languages-dark.svg` / `-light.svg` | 按仓库统计的语言占比 |

每张卡片都有明暗两套，README 用官方 `<picture>` + `prefers-color-scheme` 选择；深色版同时作为 `<img>` 兜底，供不支持 `<picture>` 的客户端显示。

## 已知约束

- 卡片是仓库内 SVG，用 Markdown 相对路径引用；内联 SVG 会被 GitHub 整块移除，故不内联。
- 不写 `class` / `style` / `id`：实测会被剥离或加前缀，布局只靠 SVG 自身 `width` 属性。
- 数据有时效性，README 页脚标注采集日期；陈旧数据由工具仓库的新鲜度门禁提醒。

## 更新记录

- 2026-11-26：首次发布。数据采集于 2026-11-26，5 个公开仓库、0 关注者、0 获星。
- 2026-11-26：线上验证通过。仓库公开、`master` 分支、根目录 README 非空；服务端渲染 HTML 的 11 项结构门禁全部通过；三张卡片在真实页面中解析为 `/xyykmkefke/xyykmkefke/raw/master/assets/*.svg`，`curl -L` 均返回 `200 image/svg+xml` 且字节数与本地一致（1539 / 2912 / 1318），未走 camo。
- 2026-11-26：加入明暗双主题。每张卡片拆成 `-dark` / `-light` 两套，README 改用 `<picture>` + `prefers-color-scheme` 切换；浅色调色板文字对比度 15.80 / 6.11 / 5.19 / 5.08:1 全部达 AA 以上，功能性图形（进度条轨道）由 1.43:1 提升到 3.41:1。发布前用 `POST /markdown` 确认 `<picture>`、`source`、`srcset`、相对路径 `src`、`alt` 全部保留。
