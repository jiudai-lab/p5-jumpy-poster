# 超级跳跳岛 · p5.js 动态海报

用 **p5.js** 实时绘制的竖版动态海报（960 × 1280 逻辑画布），主题取自同名横版跳跃小游戏《超级跳跳岛》。
纯前端、零构建，双击 `index.html` 即看。

## 在线预览

- **动态海报**：https://jiudai-lab.github.io/p5-jumpy-poster/
- **游戏本体**：https://jiudai-lab.github.io/super-jumpy-island/

## 动画内容

| 元素 | 动效 |
| --- | --- |
| 天空 | 渐变随时间轻微呼吸 |
| 太阳 | 光晕脉动 + 12 道光芒缓慢旋转 |
| 云层 | 6 层不同速度的视差漂移，带上下浮动 |
| 远山 / 近丘 | 双层视差滚动 |
| 灌木 | 贴着山丘轮廓随波峰移动 |
| 角色 | 循环跳跃：挤压拉伸、影子缩放、落地扬尘 |
| 金币 | 沿抛物线排列，正弦上下浮动 + 绕 Y 轴翻转（缩放 x） |
| 标题 | 五个汉字逐字错相上下起伏，底板轻微摆动 |
| 徽章 | 「10 大关卡」贴纸式摇摆 |
| 粒子 | 26 个十字闪光随机闪烁 |
| 底部 | 标语与链接呼吸浮动 |

## 文件

```
index.html          # 海报本体（p5.js sketch，单文件）
p5.min.js           # p5.js v1.9.4（本地依赖，离线可用）
poster-motion.mp4   # 12 秒动画录屏（1440×1920，H.264，10 秒）
README.md
```

## 本地运行

```bash
git clone https://github.com/jiudai-lab/p5-jumpy-poster.git
cd p5-jumpy-poster
```

直接用浏览器打开 `index.html`。p5.js 已内置在仓库里，不需要联网。

## 录屏是怎么做的

没有用 OBS，也没有 ffmpeg，而是让浏览器自己录：

```js
const stream = canvas.captureStream(60);            // 抓取 canvas 每帧
const rec = new MediaRecorder(stream, {
  mimeType: 'video/mp4;codecs=avc1.42E01E',         // 直接出 H.264 MP4
  videoBitsPerSecond: 5_000_000
});
rec.ondataavailable = e => chunks.push(e.data);
rec.onstop = () => {/* Blob → dataURL → 落盘 */};
rec.start(250);
setTimeout(() => rec.stop(), 10000);
```

关键点：

- `canvas.captureStream()` 抓的是 canvas 后备缓冲，**不受窗口遮挡/缩放影响**，比屏幕录制更干净；
- 录制前把 `pixelDensity(1.5)`，画布变成 1440×1920，兼顾清晰度和体积；
- 定长录制（10s）+ 分片读取 dataURL，避免一次传输过大的字符串；
- MediaRecorder 的 MP4 带完整时长元数据，进度条可拖动。

## 踩坑记录

- `textFont('system-ui, "Microsoft YaHei", sans-serif')` 这种带引号的 CSS 字族列表会让 p5 拼出非法的 `canvas.font`，浏览器**静默回退到 10px 默认字体**，中文标题会突然变成芝麻大小。改成 `textFont('sans-serif')` 即可。
- 大字号中文要用 `textStyle(BOLD)`，否则笔画过细，缩小看会糊。

## 许可

MIT License。
