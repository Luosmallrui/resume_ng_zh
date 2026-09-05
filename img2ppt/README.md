# 图转 PPT

纯前端单页应用：上传表格照片 / 数据图表 / 流程图图片，调用多模态大模型解析为结构化 JSON，页面内可编辑预览，最后一键导出**原生可编辑**的 PPTX。

BYOK 模式：API Key 由用户自己填写，仅保存在浏览器 localStorage，浏览器直连模型服务商接口，不经过任何第三方服务器。

## 功能

- 上传图片（点击 / 拖拽），缩略图预览
- 类型选择：自动判断 / 表格 / 数据图表 / 流程图
- 调用 OpenAI 兼容接口（`POST {baseUrl}/chat/completions`，默认阿里云 DashScope 兼容模式 + `qwen-vl-max`），`response_format: json_object` 失败时自动降级重试
- 结构化 JSON 预览（可折叠）+ SVG 可视化预览，标题和图中文字可点击直接编辑，修改同步到导出结果
- 导出 PPTX（PptxGenJS）：表格 → 原生表格，图表 → 原生可编辑图表（bar/line/pie），流程图 → 圆角矩形 + 带箭头连线
- 「使用模拟数据测试」开关：无 Key 时验证预览与导出完整链路
- 容错：非 JSON 输出展示原文；网络/鉴权/CORS 错误给出排查提示

## 使用方法

1. 打开「API 设置」，填入 Base URL、API Key、模型名（或勾选「使用模拟数据测试」）
2. 上传图片，选择类型（默认自动判断）
3. 点击「开始转换」，等待解析
4. 在预览中点击文字修正识别结果
5. 点击「导出 PPTX」，得到 `converted.pptx`

## 本地打开

单文件、无构建步骤，直接用浏览器打开 `index.html` 即可（导出依赖 jsDelivr CDN 加载 PptxGenJS，需联网）。也可以起个静态服务：

```bash
cd img2ppt && python3 -m http.server 8000
# 访问 http://localhost:8000
```

## 部署到 GitHub Pages

纯静态，直接把本目录推到仓库即可：

```bash
git init && git add index.html README.md && git commit -m "img2ppt"
git remote add origin <你的仓库地址> && git push -u origin main
```

然后在仓库 Settings → Pages → Source 选 `main` 分支根目录，保存后访问 `https://<用户名>.github.io/<仓库名>/`。

## 已知限制

- **CORS 风险**：浏览器直连模型接口受对方 CORS 策略限制。如果 DashScope 直连被浏览器拦截（控制台报 CORS 错误），可在 Base URL 处填自建代理地址（如 Cloudflare Workers / Nginx 转发并附加 CORS 头）。
- 流程图布局依赖模型给出的相对坐标，复杂图可能重叠，预览中可改文字但不能拖动节点位置。
- 超大图片未做压缩，可能超出模型上下文或导致请求缓慢。
