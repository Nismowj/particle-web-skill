# 粒子网页 · 网页粒子效果技能

用 [tsParticles v4](https://particles.js.org/) 给网页加粒子特效的**本地自包含**技能包（零 CDN、零 npm install，复制即用）。

## 三种效果（已实测通过）
1. **背景粒子网络** — 漂浮 + 自动连线，鼠标悬停排斥，点击扩散
2. **鼠标交互粒子** — 移动发射彩色粒子，点击爆发（数量封顶防卡顿）
3. **文字粒子** — 粒子排列成文字轮廓

## 关键特性
- 依赖打包在 `assets/`，离线可用
- 已踩平所有坑：v4 API 差异（`load({id,options})`、需 `loadFull`）、容器必须撑满尺寸、`file://` 限制需 localhost 服务器

## 用法
把 `assets/` 两个 js 与模板 html 放同目录，按 `SKILL.md` 配置即可。详见 `SKILL.md`。

## 目录
- `SKILL.md` 完整文档 + 踩坑表 + 自定义速查
- `assets/tsparticles.engine.min.js` 引擎
- `assets/tsparticles.bundle.min.js` 全量功能包
- `templates/particles-basic.html` 单效果模板（最常用）
- `templates/particles-full.html` 三效果完整模板
