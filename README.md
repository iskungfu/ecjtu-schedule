# ECJTU 课程表

华东交通大学课程表，单文件 HTML + 原生 JS，无构建工具、无框架，部署在 GitHub Pages。

**在线访问**：https://iskungfu.github.io/ecjtu-schedule/

## 功能

- 周一到周日 7 列课表（周末列降饱和显示），支持 17 周周次切换（第 9 周实践周自动跳过）
- 当前时间红线：按节次时间比例定位，切周 / 窗口缩放时自动重画
- 「下一节课」提示条：显示课名、节次、教室与倒计时
- 「导入」功能：粘贴教务系统 API 链接或页面内容，自动解析 HTML / JSON / ICS 格式
- 主题色设置：学院蓝 / 森林绿 / 极简灰，本地保存
- PWA：可添加到手机桌面，离线可用

## 如何导入自己的课表

打开页面后点击右上角「导入」按钮，两种方式任选：

### 方式一：粘贴 API 链接（推荐）

把教务系统课表接口地址粘贴到输入框，点击「立即导入」：

```
https://jwxt.ecjtu.edu.cn/weixin/CalendarServlet?weiXinID=<你的ID>&date=YYYY-MM-DD
```

`weiXinID` 在微信版教务系统的课表链接里可以找到。链接含 `CalendarServlet` 时可勾选「抓取整个学期」，会逐天拉取全学期并自动合并同一门课的周次。

导入成功后数据保存在浏览器 `localStorage`（键名 `ecjtu_schedule_v2`），刷新不丢。点右上角「⟳ 刷新」按钮可清除本地数据、恢复官方同步数据。

### 方式二：直接粘贴内容

如果链接跨域拉不通（见下文），把教务系统课表页面的源码（浏览器里 `Ctrl+U` 全选复制）、或接口返回的 HTML / JSON / ICS 文本，直接粘贴到「或者直接粘贴内容」输入框导入。

## CORS 跨域说明与解决方案

教务系统 `jwxt.ecjtu.edu.cn` 不返回 `Access-Control-Allow-Origin` 响应头，部署在 GitHub Pages 上的页面**无法直接 fetch 该接口**（浏览器跨域限制，不是课表的 bug）。导入时会给出红色提示，可用以下两个方案解决：

### 方案 A：本地代理（会用命令行）

在本地起一个小代理，把请求转发到教务系统：

```bash
npx cors-anywhere-server 8080
# 或
npx local-cors-proxy --proxyUrl https://jwxt.ecjtu.edu.cn
```

然后把导入链接换成 `http://localhost:8080/https://jwxt.ecjtu.edu.cn/weixin/CalendarServlet?...`，导入成功后数据已存进 localStorage，之后不再需要代理。

### 方案 B：手动粘贴（零门槛）

用手机/电脑浏览器直接打开教务系统课表页面（同源访问不受 CORS 限制），复制页面内容粘贴到导入弹窗的文本框即可。

## 开发

```bash
# 本地预览
python3 -m http.server 8000
# 打开 http://localhost:8000
```

目录结构：`index.html`（全部界面与逻辑）、`sw.js`（Service Worker 缓存）、`manifest.webmanifest`（PWA 配置）、`course_data.json`（官方同步课表数据）。

> 注意：直接双击用 `file://` 打开也能用（走内置 fallback 数据），但 PWA 与 fetch 导入需要 http(s) 环境。