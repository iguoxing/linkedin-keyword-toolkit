# LinkedIn 获客工具箱

<p align="center">
  <a href="https://iguoxing.github.io/linkedin-keyword-toolkit/">
    <img src="public/favicon.svg" alt="Logo" width="80" />
  </a>
</p>

<p align="center">
  <a href="https://iguoxing.github.io/linkedin-keyword-toolkit/">
    <img src="https://img.shields.io/badge/在线演示-Live-brightgreen?style=flat-square" alt="在线演示" />
  </a>
  <img src="https://img.shields.io/badge/Vue-3.5-42b883?style=flat-square&logo=vue.js" alt="Vue 3" />
  <img src="https://img.shields.io/badge/Vite-6.2-646cff?style=flat-square&logo=vite" alt="Vite" />
  <img src="https://img.shields.io/github/license/iguoxing/linkedin-keyword-toolkit?style=flat-square" alt="MIT License" />
  <img src="https://img.shields.io/github/last-commit/iguoxing/linkedin-keyword-toolkit?style=flat-square" alt="Last Commit" />
</p>

<p align="center">
  <strong>🚀 免费 · 开源 · 无需注册 · 即开即用</strong><br/>
  8 合 1 的 LinkedIn B2B 获客工具集合，助力销售高效挖掘潜客、优化个人品牌、自动化日常运营。
</p>

---

## 🌐 在线使用

👉 **[https://iguoxing.github.io/linkedin-keyword-toolkit/](https://iguoxing.github.io/linkedin-keyword-toolkit/)**

无需安装，打开即用。数据保存在浏览器本地，不上传服务器。

---

## ✨ 功能一览

| # | 工具 | 核心能力 |
|---|------|----------|
| 1 | 🔍 **布尔搜索生成器** | 可视化拼接 `AND` / `OR` / `NOT` 搜索语句，一键复制或跳转 LinkedIn 搜索 |
| 2 | 🔑 **关键词拓词器** | 输入行业/角色，自动生成高权重 LinkedIn 搜索关键词，支持中英文映射 |
| 3 | 📝 **资料优化器** | 分析目标职位，给出 Headline / About / Skills 优化建议，提升个人资料曝光度 |
| 4 | 📄 **JD 分析器** | 粘贴职位描述，自动提取高频技能词、软技能、硬技能，支持简历匹配度检测 |
| 5 | 💼 **职位探索器** | 输入目标岗位，探索 LinkedIn 上相关职位、公司规模、技能要求分布 |
| 6 | 🏢 **竞品分析器** | 输入竞品公司名，分析其 LinkedIn 动态、员工规模、招聘活跃度 |
| 7 | ✉️ **开发信改写器** | 输入中文需求，AI 生成 3 种风格英文开发信，内置垃圾邮件风险检测 |
| 8 | 👣 **访客足迹挖掘** | 个人主页转化力评分 + 潜客筛选雷达 + 自动化每日触达计划 + 转化漏斗追踪 |

---

## 🎯 适用人群

- **B2B 销售 / SDR / AE**：高效挖掘潜客，批量触达，提升回复率
- **跨境电商 / 外贸业务员**：通过 LinkedIn 开发海外客户
- **招聘顾问 / Headhunter**：精准搜索候选人，优化个人品牌吸引被动人才
- **创业者 / 独立开发者**：用 LinkedIn 冷启动获客，零预算增长

---

## 🚀 快速开始

### 在线使用（推荐）
直接访问 👉 **[https://iguoxing.github.io/linkedin-keyword-toolkit/](https://iguoxing.github.io/linkedin-keyword-toolkit/)**

### 本地开发

```bash
# 克隆项目
git clone https://github.com/iguoxing/linkedin-keyword-toolkit.git
cd linkedin-keyword-toolkit

# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

访问 `http://localhost:5173` 即可使用。

---

## 🔧 核心技术栈

| 技术 | 说明 |
|------|------|
| **Vue 3** | Composition API + `<script setup>` |
| **Vite 6** | 极速构建与热更新 |
| **纯 CSS** | 无第三方 UI 库，轻量可控 |
| **GitHub Actions** | 自动构建并部署到 GitHub Pages |
| **localStorage** | 数据持久化，不依赖后端 |

---

## 📦 项目结构

```
linkedin-keyword-toolkit/
├── public/
│   ├── robots.txt          # SEO 爬虫指引
│   └── sitemap.xml         # 搜索引擎站点地图
├── src/
│   ├── components/
│   │   ├── BooleanSearch.vue      # 布尔搜索生成器
│   │   ├── KeywordTool.vue        # 关键词拓词器
│   │   ├── ProfileOptimizer.vue   # 资料优化器
│   │   ├── JDKeywordAnalyzer.vue  # JD 分析器
│   │   ├── JobExplorer.vue       # 职位探索器
│   │   ├── CompetitorAnalysis.vue # 竞品分析器
│   │   ├── OutreachWriter.vue     # 开发信改写器
│   │   └── ProfileVisiting.vue   # 访客足迹挖掘
│   ├── App.vue
│   └── main.js
├── index.html              # SEO 优化（OG/Twitter/JSON-LD）
└── README.md
```

---

## 🔑 关于 AI 功能（开发信改写）

开发信改写器支持接入 AI 模型，目前兼容：

- **Google Gemini API**（推荐，免费额度充足）
  - 获取免费 Key：[Google AI Studio](https://aistudio.google.com/apikey)
  - 免费额度：每分钟 15 次，每天 1500 次
- **任何 OpenAI 兼容 API**（自定义 endpoint + API Key）

> 💡 未配置 API 时，系统会使用内置英文模板生参考示例，仍可正常使用。

---

## 📊 SEO 与部署

本项目已针对搜索引擎优化：

- ✅ `index.html` 含 Open Graph / Twitter Card / JSON-LD 结构化数据
- ✅ `robots.txt` 指引爬虫
- ✅ `sitemap.xml` 提交站点地图
- ✅ 自动部署：push 到 `main` 分支即触发 GitHub Actions 构建并发布

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

```bash
# Fork 本仓库后
git checkout -b feature/your-feature
# 修改代码...
git commit -m "feat: 描述你的改动"
git push origin feature/your-feature
# 提交 Pull Request
```

---

## 📄 开源协议

[MIT License](LICENSE) — 免费使用、修改和分发。

---

## ⚠️ 免责声明

本工具仅辅助生成 LinkedIn 运营相关内容，请遵守 LinkedIn 用户协议及当地法律法规。开发者不对使用本工具产生的任何后果负责。

---

## ⭐ Star History

如果这个工具对你有帮助，欢迎给个 Star ⭐ — 你的支持是持续更新的动力！

<p align="center">
  <a href="https://github.com/iguoxing/linkedin-keyword-toolkit">
    <img src="https://img.shields.io/github/stars/iguoxing/linkedin-keyword-toolkit?style=social" alt="GitHub Stars" />
  </a>
</p>

---

> Built with ❤️ by [iguoxing](https://github.com/iguoxing) · Powered by [WorkBuddy](https://www.workbuddy.ai/)
