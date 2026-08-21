---
name: 粒子网页
version: 1.0.0
description: 用 tsParticles v4 给网页加粒子效果（背景粒子/鼠标交互/文字粒子），本地自包含、零 CDN 依赖。覆盖所有实测踩坑点。
---

# 粒子网页 技能

用 **tsParticles v4** 给页面加粒子特效。三种效果已实测通过：
1. **背景粒子网络** — 漂浮+自动连线，鼠标悬停排斥，点击扩散
2. **鼠标交互粒子** — 移动发射彩色粒子，点击爆发（数量封顶防卡顿）
3. **文字粒子** — 粒子排列成文字轮廓

依赖已打包在 `assets/`，**无需联网、无需 npm install**，复制即用。

## 文件结构

```
粒子网页/
├── SKILL.md              # 本文件
├── assets/
│   ├── tsparticles.engine.min.js   # 引擎（必须第一个加载）
│   └── tsparticles.bundle.min.js   # 全量功能包（含 loadFull）
└── templates/
    ├── particles-basic.html  # 单效果模板（背景粒子，直接改内容）
    └── particles-full.html   # 三效果完整模板
```

## 使用步骤

1. 把 `assets/` 下两个 `.js` 复制到你的页面同目录
2. 页面引入（**顺序不能反**）：
   ```html
   <script src="tsparticles.engine.min.js"></script>
   <script src="tsparticles.bundle.min.js"></script>
   ```
3. 加一个容器 `<div id="tsparticles"></div>`
4. 初始化脚本（见下方"标准代码骨架"）

## ⚠️ 实测踩坑（必看）

| 坑 | 现象 | 正确做法 |
|----|------|---------|
| v4 版本号 | 搜到的 `2.12.0` 不存在 | 用 `4.3.2`；CDN 全量包路径 `tsparticles@4/tsparticles.bundle.min.js` |
| 必须先注册 | 只引 bundle 不调 `loadFull`，无报错但不渲染 | `await loadFull(tsParticles)` 必须最先执行 |
| **API 签名** | `tsParticles.load('id', {...})` 静默失败，canvas 数量 0 | 第一个参数必须是**对象** `{ id: 'xxx', options: {...} }` |
| **容器尺寸** | div 为空 → 高度塌成 0 → 不渲染 | 容器必须 `position:absolute; inset:0; width:100%; height:100%` 或显式高度 |
| `file://` 协议 | 跨域拦截，CDN/本地模块都加载失败 | 用 localhost 服务器跑（`python -m http.server` 或 node http） |
| 点击爆炸 mode | `mode:'explode'` 无效 | 用 `mode:'push'` + `modes.push.quantity` 批量生成 |
| 无限生成卡顿 | trail 模式持续累积粒子 | 加 `particles.number.limit`（如 200）封顶 |

## 标准代码骨架

```html
<script src="tsparticles.engine.min.js"></script>
<script src="tsparticles.bundle.min.js"></script>
<script>
(async () => {
  await loadFull(tsParticles);              // ① 注册功能（必须）
  await tsParticles.load({                  // ② 第一个参数是对象！
    id: 'tsparticles',                       //    容器 div 的 id
    options: {
      fullScreen: { enable: false },         //    嵌入容器而非全屏
      particles: {
        number: { value: 80, limit: 150 },   //    limit 防止卡顿
        /* ... 效果配置 ... */
      }
    }
  });
})();
</script>
```

## 三种效果配置要点

**效果一：背景粒子网络**
```js
particles: {
  number:  { value: 80, density: { enable: true } },
  color:   { value: '#60a5fa' },
  links:   { enable: true, distance: 150, color: '#60a5fa', opacity: 0.3, width: 1 },
  move:    { enable: true, speed: 1.5, outModes: 'bounce' }
},
interactivity: {
  events: { onHover: { enable: true, mode: 'repulse' }, onClick: { enable: true, mode: 'push' } },
  modes:  { repulse: { distance: 120 }, push: { quantity: 4 } }
}
```

**效果二：鼠标交互（轻量、不挡内容）**
```js
particles: {
  number:  { value: 0, limit: 200 },        // 初始 0，靠交互生成；limit 封顶
  color:   { value: ['#f472b6','#a78bfa','#60a5fa','#34d399'] },
  opacity: { value: { min: 0.2, max: 0.5 } }, // 淡一些
  size:    { value: { min: 1, max: 3 } },      // 小一些
  move:    { enable: true, speed: { min: 1, max: 3 }, outModes: 'out' }
},
interactivity: {
  events: { onHover: { enable: true, mode: 'trail' }, onClick: { enable: true, mode: 'push' } },
  modes:  { trail: { lienAge: 30, particles: { number: { value: -4 } } }, push: { quantity: 30 } }
}
```

**效果三：文字粒子**
```js
particles: {
  number:  { value: 3000 },
  color:   { value: '#fbbf24' },
  shapeText: {
    text: {
      value: ['粒子特效', 'tsParticles'],  // 改成你的文字（数组可多行）
      font: 'Arial Black', height: 100
    }
  }
}
```

## 本地预览命令

```bash
# 在页面目录执行（任选其一）
python -m http.server 8000
# 或
npx serve
```
然后浏览器打开 `http://localhost:8000`。

## 自定义速查

| 需求 | 改哪里 |
|------|--------|
| 粒子数量 | `particles.number.value` |
| 颜色 | `particles.color.value`（支持数组多色） |
| 大小 | `particles.size.value` |
| 速度 | `particles.move.speed` |
| 连线 | `particles.links.enable` |
| 鼠标悬停 | `interactivity.events.onHover.mode` |
| 点击 | `interactivity.events.onClick.mode` |
| 全屏铺满 | `fullScreen: { enable: true }`（去掉容器 div） |
