<template>
  <div class="outreach-writer">
    <h2>✉️ LinkedIn 开发信 AI 改写器</h2>
    <p class="subtitle">输入中文需求，AI 自动生成高转化率英文开发信，附带垃圾邮件风险检测</p>

    <!-- API 设置 -->
    <div class="api-settings">
      <label>AI API 配置（可选，留空使用内置模拟模式）：</label>
      <div class="api-row">
        <input v-model="apiEndpoint" placeholder="API Endpoint（如 https://api.openai.com/v1/chat/completions）" />
        <input v-model="apiKey" type="password" placeholder="API Key（可选）" />
        <button class="btn-link" @click="testAPI">测试连接</button>
      </div>
      <p v-if="apiStatus" :class="apiStatus.type">{{ apiStatus.msg }}</p>
    </div>

    <!-- 输入区域 -->
    <div class="input-section">
      <label>中文开发信需求描述：</label>
      <textarea v-model="chineseInput" placeholder="例：我是做机械出口的，想联系美国的大型分销商，介绍我们的产品和价格优势，希望他们回复..." rows="5"></textarea>

      <div class="options-row">
        <div class="option-group">
          <label>目标行业：</label>
          <input v-model="industry" placeholder="如：机械设备、电子、纺织" />
        </div>
        <div class="option-group">
          <label>目标角色：</label>
          <input v-model="targetRole" placeholder="如：采购经理、CEO、业务拓展" />
        </div>
        <div class="option-group">
          <label>邮件风格偏好：</label>
          <select v-model="stylePref">
            <option value="balanced">均衡（3种风格）</option>
            <option value="direct">直接高效型为主</option>
            <option value="friendly">友好亲和型为主</option>
            <option value="value">价值驱动型为主</option>
          </select>
        </div>
      </div>

      <button class="btn-generate" @click="generateMessages" :disabled="!chineseInput.trim() || generating">
        {{ generating ? 'AI 生成中...' : '🚀 生成开发信' }}
      </button>
    </div>

    <!-- 结果区域 -->
    <div v-if="messages.length" class="results-section">
      <h3>📧 生成的开发信（共 {{ messages.length }} 种风格）</h3>

      <div v-for="(msg, idx) in messages" :key="idx" class="message-card" :class="msg.riskLevel">
        <div class="card-header">
          <span class="style-badge" :class="msg.style">{{ styleLabel(msg.style) }}</span>
          <span class="risk-badge" :class="msg.riskLevel">
            {{ riskLabel(msg.riskLevel) }} · 垃圾邮件风险：{{ msg.spamScore }}%
          </span>
        </div>

        <div class="message-content">
          <div class="field">
            <label>主题行：</label>
            <div class="text-block">{{ msg.subject }}</div>
          </div>
          <div class="field">
            <label>正文：</label>
            <div class="text-block" v-html="formatBody(msg.body)"></div>
          </div>
        </div>

        <div class="card-actions">
          <button class="btn-action" @click="copyMessage(msg)">📋 复制</button>
          <button class="btn-action" @click="regenerateOne(idx)">🔄 重新生成此风格</button>
          <div class="spam-analysis">
            <strong>垃圾邮件检测：</strong>
            <span :class="msg.riskLevel">{{ msg.spamAnalysis }}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- 提示词历史 -->
    <div v-if="history.length" class="history-section">
      <h3>📜 历史记录</h3>
      <div v-for="(h, idx) in history" :key="idx" class="history-item" @click="loadHistory(h)">
        <span class="history-preview">{{ h.input.slice(0, 50) }}...</span>
        <span class="history-time">{{ h.time }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const chineseInput = ref('')
const industry = ref('')
const targetRole = ref('')
const stylePref = ref('balanced')
const apiEndpoint = ref('')
const apiKey = ref('')
const apiStatus = ref(null)
const generating = ref(false)
const messages = ref([])
const history = ref([])

const SPAM_KEYWORDS = [
  'free', 'guaranteed', 'no obligation', 'act now', 'limited time',
  'cash', 'money', 'income', 'earn extra', 'work from home',
  'buy now', 'order now', 'click here', 'urgent', 'important',
  'congratulations', 'winner', 'prize', '!!!', '$$$',
  'best price', 'cheapest', 'discount', 'promotion', 'offer'
]

const styleLabel = (style) => {
  const map = { direct: '🎯 直接高效型', friendly: '🤝 友好亲和型', value: '💡 价值驱动型' }
  return map[style] || style
}

const riskLabel = (level) => {
  const map = { low: '✅ 低风险', medium: '⚠️ 中风险', high: '🚨 高风险' }
  return map[level] || level
}

const formatBody = (body) => {
  return body.replace(/\n/g, '<br>').replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
}

// 垃圾邮件检测
const analyzeSpam = (text) => {
  const lower = text.toLowerCase()
  let score = 0
  const flags = []

  SPAM_KEYWORDS.forEach(word => {
    if (lower.includes(word)) {
      score += 8
      flags.push(`含垃圾邮件词汇: "${word}"`)
    }
  })

  // 全大写检测
  const capsRatio = (text.match(/[A-Z]/g) || []).length / Math.max(text.length, 1)
  if (capsRatio > 0.3) {
    score += 15
    flags.push('过多大写字母')
  }

  // 感叹号检测
  const exclCount = (text.match(/!/g) || []).length
  if (exclCount > 2) {
    score += exclCount * 3
    flags.push(`过多感叹号 (${exclCount}个)`)
  }

  // 长度检测
  if (text.length > 800) {
    score += 10
    flags.push('正文过长（>800字符）')
  } else if (text.length < 100) {
    score += 5
    flags.push('正文过短（<100字符）')
  }

  // 链接检测
  if (text.includes('http') || text.includes('www.')) {
    score += 20
    flags.push('包含链接（开发信中建议避免）')
  }

  score = Math.min(score, 100)

  let riskLevel, analysis
  if (score <= 20) {
    riskLevel = 'low'
    analysis = `✅ 低风险（${score}分）：此邮件不太可能被标记为垃圾邮件。保持简洁、个性化。`
  } else if (score <= 50) {
    riskLevel = 'medium'
    analysis = `⚠️ 中风险（${score}分）：建议修改以下问题：${flags.join('; ')}`
  } else {
    riskLevel = 'high'
    analysis = `🚨 高风险（${score}分）：极易被标记为垃圾邮件！请修改：${flags.join('; ')}`
  }

  return { score, riskLevel, analysis, flags }
}

// 调用 AI API
const callAI = async (prompt) => {
  // 如果没有配置 API，使用内置模拟模式
  if (!apiEndpoint.value) {
    return simulateAI(prompt)
  }

  const body = {
    model: 'gpt-3.5-turbo',
    messages: [{ role: 'user', content: prompt }],
    temperature: 0.8,
  }

  const headers = { 'Content-Type': 'application/json' }
  if (apiKey.value) {
    headers['Authorization'] = `Bearer ${apiKey.value}`
  }

  const res = await fetch(apiEndpoint.value, {
    method: 'POST',
    headers,
    body: JSON.stringify(body),
  })

  if (!res.ok) throw new Error(`API 错误 ${res.status}: ${await res.text()}`)
  const data = await res.json()
  return data.choices[0].message.content
}

// 模拟 AI 生成（内置 fallback）
const simulateAI = async (prompt) => {
  // 模拟网络延迟
  await new Promise(r => setTimeout(r, 1500))
  return `[SIMULATED AI OUTPUT]
请配置真实 AI API 以获得最佳效果。
当前为模拟模式，生成的内容仅供参考。
建议填入 OpenAI / Claude / 国内大模型 API endpoint 和 key。`
}

// 生成单条消息
const generateSingle = async (style, input, industryVal, roleVal) => {
  const stylePrompt = {
    direct: '直接高效型：开门见山，快速说明价值，尊重对方时间，邮件长度控制在100词以内。',
    friendly: '友好亲和型：以共同话题或赞美开头，建立人际关系，自然过渡到商业话题，语气温暖。',
    value: '价值驱动型：以对方痛点或行业趋势切入，展示专业知识，提供明确价值主张，引导回复。'
  }

  const prompt = `你是一位专业的 LinkedIn 外贸开发信专家。请根据以下信息生成一封高转化率的英文开发信。

目标风格：${stylePrompt[style]}

中文需求描述：${input}
目标行业：${industryVal || '通用'}
目标角色：${roleVal || '业务负责人'}

请按以下格式输出（不要输出其他内容）：
SUBJECT: [主题行，简洁有力，不超60字符]
BODY:
[正文，3-5句话，包含个人化开头、价值主张、明确CTA]

要求：
1. 主题行避免垃圾邮件词汇（free, guarantee, !!! 等）
2. 正文个性化，提及对方公司或行业
3. CTA 明确但不强硬
4. 总长度不超过150词`

  const output = await callAI(prompt)

  // 解析输出
  const subjectMatch = output.match(/SUBJECT:\s*(.+)/i)
  const bodyMatch = output.match(/BODY:\s*([\s\S]+)/i)

  let subject = subjectMatch ? subjectMatch[1].trim() : `Partnership Opportunity - ${industryVal || 'Business'}`
  let body = bodyMatch ? bodyMatch[1].trim() : output.replace(/SUBJECT:.*\n?/i, '').trim()

  // 如果是模拟模式，生成更真实的内容
  if (!apiEndpoint.value) {
    const templates = getTemplates(style, input, industryVal, roleVal)
    subject = templates.subject
    body = templates.body
  }

  const fullText = subject + ' ' + body
  const spam = analyzeSpam(fullText)

  return { style, subject, body, ...spam }
}

// 内置模板（模拟模式使用）
const getTemplates = (style, input, industry, role) => {
  const industryPhrase = industry ? ` in the ${industry} industry` : ''
  const rolePhrase = role || 'decision-maker'

  const templates = {
    direct: {
      subject: `Quick question about ${industry || 'your business'}`,
      body: `Hi [Name],

I came across ${rolePhrase === 'CEO' ? 'your company' : 'your profile'}${industryPhrase} and was impressed by your recent growth.

We've helped companies like [Similar Company] reduce costs by 20-30% while maintaining quality. I'd love to share how we might do the same for you.

Would you be open to a brief 10-minute chat this week?

Best,
[Your Name]`
    },
    friendly: {
      subject: `Hello from [Your Company] – ${industry || 'Business'} solutions`,
      body: `Hi [Name],

I hope this message finds you well! I've been following ${industry || 'your industry'} for a while and really admired what you've built at [Company].

We recently worked with a company facing similar challenges as yours, and the results were fantastic. I thought you might be interested in learning how we could help streamline your [specific pain point].

No pressure at all – just wanted to introduce myself and see if there's a fit.

Warmly,
[Your Name]`
    },
    value: {
      subject: `3 ideas for ${rolePhrase}s in ${industry || 'your field'}`,
      body: `Hi [Name],

Most ${rolePhrase}s we speak with struggle with [common pain point in ${industry || 'this industry'}].

We've developed a solution that's helped over [X] companies address this exact challenge, resulting in [specific measurable outcome].

I'd love to share a quick case study that's relevant to your situation. Would that be helpful?

Cheers,
[Your Name]`
    }
  }

  return templates[style] || templates.direct
}

// 主生成函数
const generateMessages = async () => {
  generating.value = true
  messages.value = []

  try {
    let styles = []
    if (stylePref.value === 'balanced') {
      styles = ['direct', 'friendly', 'value']
    } else {
      styles = [stylePref.value, 'direct', 'friendly'].filter((v, i, a) => a.indexOf(v) === i).slice(0, 3)
    }

    for (const style of styles) {
      const msg = await generateSingle(style, chineseInput.value, industry.value, targetRole.value)
      messages.value.push(msg)
    }

    // 保存到历史
    history.value.unshift({
      input: chineseInput.value,
      time: new Date().toLocaleString('zh-CN'),
      messages: JSON.parse(JSON.stringify(messages.value))
    })
    if (history.value.length > 10) history.value.pop()

  } catch (err) {
    alert('生成失败：' + err.message)
  } finally {
    generating.value = false
  }
}

// 重新生成单条
const regenerateOne = async (idx) => {
  const msg = messages.value[idx]
  messages.value[idx] = await generateSingle(msg.style, chineseInput.value, industry.value, targetRole.value)
}

// 复制
const copyMessage = (msg) => {
  const text = `Subject: ${msg.subject}\n\n${msg.body}`
  navigator.clipboard.writeText(text)
  alert('已复制到剪贴板！')
}

// 加载历史
const loadHistory = (h) => {
  chineseInput.value = h.input
  messages.value = h.messages || []
}

// 测试 API
const testAPI = async () => {
  if (!apiEndpoint.value) {
    apiStatus.value = { type: 'info', msg: 'ℹ️ 未配置 API，将使用内置模拟模式（生成参考模板）' }
    return
  }
  try {
    apiStatus.value = { type: 'info', msg: '🔄 测试中...' }
    const testPrompt = 'Reply with just the word: OK'
    const body = {
      model: 'gpt-3.5-turbo',
      messages: [{ role: 'user', content: testPrompt }],
      max_tokens: 5,
    }
    const headers = { 'Content-Type': 'application/json' }
    if (apiKey.value) headers['Authorization'] = `Bearer ${apiKey.value}`

    const res = await fetch(apiEndpoint.value, {
      method: 'POST',
      headers,
      body: JSON.stringify(body),
    })
    if (res.ok) {
      apiStatus.value = { type: 'success', msg: '✅ API 连接成功！可以生成开发信了。' }
    } else {
      apiStatus.value = { type: 'error', msg: `❌ API 测试失败：${res.status} ${await res.text()}` }
    }
  } catch (err) {
    apiStatus.value = { type: 'error', msg: `❌ 连接失败：${err.message}` }
  }
}
</script>

<style scoped>
.outreach-writer {
  max-width: 900px;
  margin: 0 auto;
}
h2 {
  color: #0a66c2;
  margin-bottom: 4px;
}
.subtitle {
  color: #666;
  margin-bottom: 24px;
  font-size: 14px;
}
.api-settings {
  background: #f8f9fa;
  padding: 16px;
  border-radius: 8px;
  margin-bottom: 24px;
}
.api-settings label {
  font-weight: 600;
  font-size: 13px;
  display: block;
  margin-bottom: 8px;
}
.api-row {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}
.api-row input {
  flex: 1;
  min-width: 200px;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
}
.btn-link {
  background: none;
  border: 1px solid #0a66c2;
  color: #0a66c2;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
}
.api-settings .success { color: #0a8a2e; font-size: 13px; margin-top: 8px; }
.api-settings .error { color: #d93025; font-size: 13px; margin-top: 8px; }
.api-settings .info { color: #666; font-size: 13px; margin-top: 8px; }
.input-section {
  background: white;
  padding: 24px;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  margin-bottom: 24px;
}
.input-section label {
  font-weight: 600;
  display: block;
  margin-bottom: 8px;
  font-size: 14px;
}
textarea {
  width: 100%;
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  resize: vertical;
  box-sizing: border-box;
  font-family: inherit;
}
textarea:focus {
  outline: none;
  border-color: #0a66c2;
}
.options-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 12px;
  margin: 16px 0;
}
.option-group input, .option-group select {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
  box-sizing: border-box;
}
.btn-generate {
  background: linear-gradient(135deg, #0a66c2, #004182);
  color: white;
  border: none;
  padding: 14px 32px;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  width: 100%;
  transition: opacity 0.2s;
}
.btn-generate:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
.results-section {
  margin-bottom: 24px;
}
.results-section h3 {
  margin-bottom: 16px;
}
.message-card {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  margin-bottom: 16px;
  overflow: hidden;
  border-left: 4px solid #0a66c2;
}
.message-card.medium { border-left-color: #f0a500; }
.message-card.high { border-left-color: #d93025; }

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  background: #f8f9fa;
  flex-wrap: wrap;
  gap: 8px;
}
.style-badge {
  font-weight: 700;
  font-size: 14px;
  color: #0a66c2;
}
.risk-badge {
  font-size: 12px;
  padding: 4px 10px;
  border-radius: 12px;
  font-weight: 600;
}
.risk-badge.low { background: #e8f5e9; color: #2e7d32; }
.risk-badge.medium { background: #fff8e1; color: #f57f17; }
.risk-badge.high { background: #ffebee; color: #d93025; }
.message-content {
  padding: 16px;
}
.field {
  margin-bottom: 12px;
}
.field label {
  font-weight: 600;
  font-size: 12px;
  color: #666;
  display: block;
  margin-bottom: 4px;
}
.text-block {
  background: #f8f9fa;
  padding: 12px;
  border-radius: 6px;
  font-size: 14px;
  line-height: 1.6;
  white-space: pre-wrap;
  border: 1px solid #eee;
}
.card-actions {
  padding: 12px 16px;
  background: #f8f9fa;
  display: flex;
  gap: 8px;
  align-items: center;
  flex-wrap: wrap;
}
.btn-action {
  background: white;
  border: 1px solid #0a66c2;
  color: #0a66c2;
  padding: 6px 14px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
}
.spam-analysis {
  margin-left: auto;
  font-size: 12px;
  color: #666;
}
.spam-analysis .low { color: #2e7d32; font-weight: 600; }
.spam-analysis .medium { color: #f57f17; font-weight: 600; }
.spam-analysis .high { color: #d93025; font-weight: 600; }
.history-section h3 {
  margin-bottom: 12px;
}
.history-item {
  background: white;
  padding: 12px 16px;
  border-radius: 8px;
  margin-bottom: 8px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #eee;
  transition: background 0.2s;
}
.history-item:hover {
  background: #f8f9fa;
}
.history-preview {
  font-size: 13px;
  color: #333;
}
.history-time {
  font-size: 12px;
  color: #999;
}
</style>
