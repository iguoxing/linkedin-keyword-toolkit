# LinkedIn 关键词工具箱

一个基于 Vue 3 + Vite 的 LinkedIn 关键词辅助工具，包含三大功能模块，部署在 GitHub Pages。

## 功能

| 模块 | 说明 |
|------|------|
| **布尔搜索生成器** | 可视化拼接 AND/OR/NOT 语句，一键复制或直接在 LinkedIn 搜索 |
| **个人资料关键词优化器** | 输入目标职位，获取高/中/低权重关键词建议及各栏填写指南 |
| **JD 关键词分析器** | 粘贴职位描述，自动提取高频词、技术词、软技能词，支持简历匹配度检测 |

## 本地开发

```bash
npm install
npm run dev
```

## 部署

项目使用 **GitHub Actions** 在 push 到 `main` 分支时自动构建并发布到 GitHub Pages。

### 手动开启 GitHub Pages

1. 进入仓库 → **Settings → Pages**
2. Source 选择 **GitHub Actions**
3. 推送代码后 Actions 自动运行，完成后访问：
   ```
   https://<your-username>.github.io/linkedin-keyword-toolkit/
   ```

## 技术栈

- Vue 3 (Composition API)
- Vite 6
- 纯 CSS（无第三方 UI 库）
- GitHub Actions + GitHub Pages

## 开源协议

MIT
