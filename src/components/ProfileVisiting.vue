<template>
  <div class="profile-visiting">
    <h2>🔍 LinkedIn 访客足迹挖掘工作台</h2>
    <p class="subtitle">低成本、高隐蔽的 B 端获客增长黑客 — 让潜在客户主动找上门</p>

    <!-- 三步骤 Tab -->
    <div class="step-tabs">
      <button class="step-tab" :class="{active: step===1}" @click="step=1">
        <span class="step-icon">🛠️</span> 第一步：主页转化配置
      </button>
      <button class="step-tab" :class="{active: step===2}" @click="step=2">
        <span class="step-icon">🎯</span> 第二步：精准筛选雷达
      </button>
      <button class="step-tab" :class="{active: step===3}" @click="step=3">
        <span class="step-icon">🤖</span> 第三步：自动化访客计划
      </button>
    </div>

    <!-- ========== 第一步：个人主页转化配置 ========== -->
    <div v-if="step===1" class="step-content">
      <div class="info-banner blue">
        <strong>💡 核心理念：</strong>你的 LinkedIn 个人主页就是你的「落地页」。在被目标客户发现之前，先确保它能高效转化。
      </div>

      <h3>📋 个人主页转化力评分</h3>
      <p class="section-desc">逐项检查以下配置，获取转化率评分。完成度越高，回访客户转化为询盘的概率越大。</p>

      <div class="score-overview">
        <div class="score-circle" :class="scoreLevel">
          <div class="score-num">{{ totalScore }}</div>
          <div class="score-label">/ 100 分</div>
        </div>
        <div class="score-bars">
          <div v-for="cat in categories" :key="cat.name" class="score-bar-row">
            <span class="bar-label">{{ cat.icon }} {{ cat.name }}</span>
            <div class="bar-track">
              <div class="bar-fill" :style="{width: cat.score + '%'}" :class="scoreClass(cat.score)"></div>
            </div>
            <span class="bar-value">{{ cat.score }}/25</span>
          </div>
        </div>
      </div>

      <!-- Banner 评分 -->
      <div class="check-section">
        <h4>🖼️ 头图 (Banner) 配置</h4>
        <div class="check-items">
          <label class="check-item" v-for="(item, idx) in bannerChecks" :key="'b'+idx">
            <input type="checkbox" v-model="item.done" @change="calcScore" />
            <span class="check-box" :class="{done: item.done}"></span>
            <span class="check-text">
              <strong>{{ item.title }}</strong>
              <small>{{ item.desc }}</small>
            </span>
          </label>
        </div>
      </div>

      <!-- Headline 评分 -->
      <div class="check-section">
        <h4>✏️ 标题 (Headline) 配置</h4>
        <div class="check-items">
          <label class="check-item" v-for="(item, idx) in headlineChecks" :key="'h'+idx">
            <input type="checkbox" v-model="item.done" @change="calcScore" />
            <span class="check-box" :class="{done: item.done}"></span>
            <span class="check-text">
              <strong>{{ item.title }}</strong>
              <small>{{ item.desc }}</small>
            </span>
          </label>
        </div>
        <div class="headline-preview">
          <label>你的当前 Headline：</label>
          <textarea v-model="currentHeadline" placeholder="粘贴你现在的 LinkedIn 标题，我来帮你分析..." rows="2" @input="analyzeHeadline"></textarea>
          <div v-if="headlineAnalysis" class="headline-result" :class="headlineAnalysis.level">
            <strong>{{ headlineAnalysis.level === 'good' ? '✅ 优秀' : headlineAnalysis.level === 'ok' ? '⚠️ 一般' : '❌ 需改进' }}</strong>
            {{ headlineAnalysis.suggestion }}
          </div>
        </div>
      </div>

      <!-- Featured 评分 -->
      <div class="check-section">
        <h4>📌 置顶内容 (Featured) 配置</h4>
        <div class="check-items">
          <label class="check-item" v-for="(item, idx) in featuredChecks" :key="'f'+idx">
            <input type="checkbox" v-model="item.done" @change="calcScore" />
            <span class="check-box" :class="{done: item.done}"></span>
            <span class="check-text">
              <strong>{{ item.title }}</strong>
              <small>{{ item.desc }}</small>
            </span>
          </label>
        </div>
      </div>

      <!-- About 评分 -->
      <div class="check-section">
        <h4>📝 简介 (About) 配置</h4>
        <div class="check-items">
          <label class="check-item" v-for="(item, idx) in aboutChecks" :key="'a'+idx">
            <input type="checkbox" v-model="item.done" @change="calcScore" />
            <span class="check-box" :class="{done: item.done}"></span>
            <span class="check-text">
              <strong>{{ item.title }}</strong>
              <small>{{ item.desc }}</small>
            </span>
          </label>
        </div>
      </div>

      <div class="tip-banner">
        <strong>🎯 最佳实践：</strong>
        <ul>
          <li>头图放产品截图 + 一句话 Slogan，如「AI 驱动的 B2B 获客：效率提升 10 倍」</li>
          <li>标题格式：<strong>身份 + 价值主张</strong>，如「Helping B2B companies 3x pipeline | AI Sales Tools Founder」</li>
          <li>置顶内容放 30 秒 Demo 视频或免费体验链接，降低对方决策成本</li>
          <li>About 区域开头 3 行决定对方是否继续阅读，务必放上最核心的价值主张</li>
        </ul>
      </div>
    </div>

    <!-- ========== 第二步：精准筛选雷达 ========== -->
    <div v-if="step===2" class="step-content">
      <div class="info-banner green">
        <strong>🎯 核心理念：</strong>不要泛泛搜索，而是用极窄的筛选条件精准锁定高意向潜客。精准度 > 数量。
      </div>

      <h3>📡 筛选条件配置</h3>

      <div class="filter-grid">
        <!-- 目标岗位 -->
        <div class="filter-card">
          <h4>👔 目标岗位</h4>
          <p class="filter-desc">选择你要触达的决策者角色</p>
          <div class="tag-input-area">
            <div class="tag-list">
              <span v-for="(tag, idx) in targetRoles" :key="'r'+idx" class="tag">
                {{ tag }} <button class="tag-remove" @click="targetRoles.splice(idx,1)">×</button>
              </span>
            </div>
            <div class="tag-add">
              <input v-model="newRole" placeholder="输入岗位（如 Purchasing Manager）" @keyup.enter="addRole" />
              <button class="btn-add" @click="addRole">+</button>
            </div>
            <div class="quick-tags">
              <span>快速添加：</span>
              <button v-for="r in roleSuggestions" :key="r" class="quick-tag" @click="targetRoles.includes(r) || targetRoles.push(r)">{{ r }}</button>
            </div>
          </div>
        </div>

        <!-- 目标行业 -->
        <div class="filter-card">
          <h4>🏭 目标行业</h4>
          <p class="filter-desc">限定行业范围，提高精准度</p>
          <div class="tag-input-area">
            <div class="tag-list">
              <span v-for="(tag, idx) in targetIndustries" :key="'i'+idx" class="tag">
                {{ tag }} <button class="tag-remove" @click="targetIndustries.splice(idx,1)">×</button>
              </span>
            </div>
            <div class="tag-add">
              <input v-model="newIndustry" placeholder="输入行业（如 Automotive）" @keyup.enter="addIndustry" />
              <button class="btn-add" @click="addIndustry">+</button>
            </div>
            <div class="quick-tags">
              <span>快速添加：</span>
              <button v-for="ind in industrySuggestions" :key="ind" class="quick-tag" @click="targetIndustries.includes(ind) || targetIndustries.push(ind)">{{ ind }}</button>
            </div>
          </div>
        </div>

        <!-- 地区筛选 -->
        <div class="filter-card">
          <h4>🌍 目标地区</h4>
          <p class="filter-desc">限定地理位置</p>
          <div class="tag-input-area">
            <div class="tag-list">
              <span v-for="(tag, idx) in targetRegions" :key="'g'+idx" class="tag">
                {{ tag }} <button class="tag-remove" @click="targetRegions.splice(idx,1)">×</button>
              </span>
            </div>
            <div class="tag-add">
              <input v-model="newRegion" placeholder="输入地区（如 United States）" @keyup.enter="addRegion" />
              <button class="btn-add" @click="addRegion">+</button>
            </div>
            <div class="quick-tags">
              <span>快速添加：</span>
              <button v-for="r in regionSuggestions" :key="r" class="quick-tag" @click="targetRegions.includes(r) || targetRegions.push(r)">{{ r }}</button>
            </div>
          </div>
        </div>

        <!-- 活跃度筛选 -->
        <div class="filter-card highlight">
          <h4>⚡ 活跃度过滤（关键！）</h4>
          <p class="filter-desc">优先访问活跃用户，他们能即时看到你的访问提醒</p>
          <div class="activity-filters">
            <label class="radio-item" v-for="opt in activityOptions" :key="opt.value">
              <input type="radio" v-model="activityFilter" :value="opt.value" />
              <span class="radio-dot"></span>
              <span>
                <strong>{{ opt.label }}</strong>
                <small>{{ opt.desc }}</small>
              </span>
            </label>
          </div>
        </div>
      </div>

      <!-- 生成布尔搜索 -->
      <div class="search-generator">
        <h3>🔍 生成精准搜索语句</h3>
        <button class="btn-generate" @click="generateSearchQuery">生成布尔搜索语句</button>
        <div v-if="searchQuery" class="query-result">
          <div class="query-text">{{ searchQuery }}</div>
          <button class="btn-action" @click="copyText(searchQuery)">📋 复制搜索语句</button>
          <button class="btn-action" @click="openLinkedInSearch">🔗 在 LinkedIn 中搜索</button>
        </div>
      </div>

      <!-- 搜索技巧 -->
      <div class="tip-banner">
        <strong>💡 搜索技巧：</strong>
        <ul>
          <li><strong>岗位关键词变体</strong>：同一岗位有多个称呼，如「Purchasing Manager」=「Procurement Manager」=「Sourcing Manager」</li>
          <li><strong>公司规模过滤</strong>：在 LinkedIn 搜索后用左侧筛选器选择公司规模（11-50, 51-200 等）</li>
          <li><strong>活跃度优先</strong>：先访问「过去 30 天活跃」的用户，转化率是普通用户的 3-5 倍</li>
          <li><strong>每日限量</strong>：建议每天访问 30-50 个，避免触发 LinkedIn 风控</li>
        </ul>
      </div>
    </div>

    <!-- ========== 第三步：自动化访客计划 ========== -->
    <div v-if="step===3" class="step-content">
      <div class="info-banner orange">
        <strong>🤖 核心理念：</strong>每天定时、定量访问筛选出的潜客。对方看到提醒 → 产生好奇 → 回访你的主页 → 产生询盘意向。
      </div>

      <!-- ===== 自动化执行器 ===== -->
      <div class="auto-executor" :class="{running: autoRunning}">
        <h3>🚀 自动化执行器</h3>
        <p class="section-desc">导入潜客名单 → 启动执行器 → 按设定间隔自动打开下一个潜客主页 → 你只需浏览停留后点「已完成」</p>

        <!-- 执行控制面板 -->
        <div class="executor-control">
          <div class="control-main">
            <!-- 当前状态 -->
            <div class="current-target" v-if="autoRunning || autoPaused">
              <div class="target-header">
                <span class="target-counter">
                  <strong>{{ visitedToday }} / {{ dailyTarget }}</strong>
                  <small>今日进度</small>
                </span>
                <span class="target-status" :class="{paused: autoPaused}">
                  {{ autoPaused ? '⏸️ 已暂停' : '⏱️ 自动运行中' }}
                </span>
              </div>
              <!-- 进度条 -->
              <div class="progress-bar-track">
                <div class="progress-bar-fill" :style="{width: progressPercent + '%'}"></div>
              </div>
              <div class="progress-info">
                <span>{{ progressPercent }}% 完成</span>
                <span>剩余 {{ dailyTarget - visitedToday }} 人</span>
              </div>

              <!-- 当前潜客 -->
              <div v-if="currentProspect" class="current-prospect-card">
                <div class="prospect-number">第 {{ visitedToday + 1 }} 位</div>
                <div class="prospect-name">{{ currentProspect.name || '未命名' }}</div>
                <div class="prospect-url" v-if="currentProspect.url">
                  <a :href="currentProspect.url" target="_blank" class="visit-link">🔗 打开 LinkedIn 主页</a>
                </div>
              </div>

              <!-- 倒计时 -->
              <div class="countdown-area" v-if="autoRunning && !autoPaused">
                <div class="countdown-ring">
                  <svg viewBox="0 0 100 100">
                    <circle cx="50" cy="50" r="45" stroke="#e0e0e0" stroke-width="6" fill="none"/>
                    <circle cx="50" cy="50" r="45" stroke="#0a66c2" stroke-width="6" fill="none"
                      stroke-dasharray="283" :stroke-dashoffset="countdownOffset"
                      stroke-linecap="round" transform="rotate(-90 50 50)"
                      style="transition: stroke-dashoffset 1s linear;"/>
                  </svg>
                  <div class="countdown-text">{{ countdownDisplay }}</div>
                </div>
                <div class="countdown-label">下一个提醒倒计时</div>
              </div>
            </div>

            <!-- 未启动状态 -->
            <div v-if="!autoRunning && !autoPaused" class="idle-state">
              <div class="idle-icon">📋</div>
              <div class="idle-text">准备就绪，导入潜客名单后启动执行器</div>
            </div>
          </div>

          <!-- 操作按钮 -->
          <div class="control-actions">
            <template v-if="!autoRunning && !autoPaused">
              <button class="btn-executor start" @click="startAutoExecutor" :disabled="todayQueue.length === 0">
                ▶️ 启动执行器
              </button>
            </template>
            <template v-else>
              <button class="btn-executor done" @click="markCurrentDone" v-if="!autoPaused">
                ✅ 已浏览完成，下一位
              </button>
              <button class="btn-executor skip" @click="skipCurrent" v-if="!autoPaused">
                ⏭️ 跳过此位
              </button>
              <button class="btn-executor pause" @click="togglePause" v-if="!autoPaused">
                ⏸️ 暂停
              </button>
              <button class="btn-executor resume" @click="togglePause" v-else>
                ▶️ 继续
              </button>
              <button class="btn-executor stop" @click="stopAutoExecutor">
                ⏹️ 结束今天任务
              </button>
            </template>
          </div>
        </div>

        <!-- 今日完成率快速统计 -->
        <div class="daily-stats-mini" v-if="visitedToday > 0">
          <div class="mini-stat">
            <span class="mini-num">{{ visitedToday }}</span>
            <span class="mini-label">已访问</span>
          </div>
          <div class="mini-stat">
            <span class="mini-num">{{ skippedToday }}</span>
            <span class="mini-label">已跳过</span>
          </div>
          <div class="mini-stat">
            <span class="mini-num">{{ todayQueue.length - visitedToday - skippedToday }}</span>
            <span class="mini-label">队列剩余</span>
          </div>
          <div class="mini-stat">
            <span class="mini-num">{{ sessionDuration }}</span>
            <span class="mini-label">本次耗时</span>
          </div>
        </div>
      </div>

      <!-- ===== 潜客队列管理 ===== -->
      <div class="queue-section">
        <h3>📋 潜客队列管理</h3>

        <!-- 导入区 -->
        <div class="import-area">
          <div class="import-tabs">
            <button class="import-tab" :class="{active: importMode === 'url'}" @click="importMode='url'">🔗 批量导入 URL</button>
            <button class="import-tab" :class="{active: importMode === 'manual'}" @click="importMode='manual'">✏️ 手动添加</button>
          </div>

          <!-- URL 批量导入 -->
          <div v-if="importMode === 'url'" class="import-url">
            <p class="import-desc">粘贴多个 LinkedIn 个人主页 URL，每行一个：</p>
            <textarea v-model="bulkUrls" placeholder="https://www.linkedin.com/in/john-smith/
https://www.linkedin.com/in/sarah-johnson/
https://www.linkedin.com/in/michael-brown/
..." rows="6"></textarea>
            <div class="import-actions">
              <button class="btn-import" @click="importFromUrls">📥 导入到队列</button>
              <button class="btn-clear-queue" @click="clearQueue">🗑️ 清空队列</button>
            </div>
          </div>

          <!-- 手动添加 -->
          <div v-if="importMode === 'manual'" class="import-manual">
            <div class="manual-row">
              <input v-model="manualName" placeholder="姓名（如 John Smith）" />
              <input v-model="manualUrl" placeholder="LinkedIn URL（可选）" />
              <select v-model="manualRole">
                <option value="">无备注</option>
                <option value="Purchasing Manager">Purchasing Manager</option>
                <option value="CEO">CEO</option>
                <option value="Founder">Founder</option>
                <option value="CTO">CTO</option>
                <option value="VP Sales">VP Sales</option>
              </select>
              <button class="btn-add-manual" @click="addManualProspect">添加</button>
            </div>
          </div>
        </div>

        <!-- 队列列表 -->
        <div v-if="todayQueue.length > 0" class="queue-list">
          <div class="queue-header">
            <strong>今日队列</strong>
            <span class="queue-count">共 {{ todayQueue.length }} 位潜客</span>
          </div>
          <div class="queue-items">
            <div v-for="(p, idx) in todayQueue" :key="idx"
              class="queue-item" :class="{visited: p.status === 'visited', skipped: p.status === 'skipped', current: idx === currentQueueIndex && autoRunning}">
              <div class="queue-item-left">
                <span class="queue-idx">{{ idx + 1 }}</span>
                <span class="queue-status-icon">
                  {{ p.status === 'visited' ? '✅' : p.status === 'skipped' ? '⏭️' : p.status === 'current' ? '👁️' : '⬜' }}
                </span>
                <div class="queue-item-info">
                  <strong>{{ p.name }}</strong>
                  <small v-if="p.role">{{ p.role }}</small>
                  <small v-if="p.url" class="queue-url">{{ truncateUrl(p.url) }}</small>
                </div>
              </div>
              <div class="queue-item-right">
                <a v-if="p.url" :href="p.url" target="_blank" class="queue-link">打开</a>
                <button v-if="p.status === 'pending'" class="queue-remove" @click="removeFromQueue(idx)">×</button>
              </div>
            </div>
          </div>
        </div>

        <div v-else class="empty-state">
          <p>队列为空。请通过上方导入 LinkedIn URL 或手动添加潜客。</p>
          <p class="empty-tip">提示：在 LinkedIn 搜索结果页面，复制目标用户的 URL 粘贴到批量导入框中。</p>
        </div>
      </div>

      <!-- ===== 每日计划配置 ===== -->
      <div class="plan-config">
        <h3>📅 每日访问计划</h3>
        <div class="config-row">
          <div class="config-item">
            <label>每日访问数量</label>
            <input type="number" v-model.number="dailyTarget" min="10" max="100" />
            <small>推荐 30-50，安全范围</small>
          </div>
          <div class="config-item">
            <label>访问间隔（秒）</label>
            <input type="number" v-model.number="visitInterval" min="30" max="300" />
            <small>推荐 60-120 秒，模拟人工</small>
          </div>
          <div class="config-item">
            <label>执行时段</label>
            <div class="time-range">
              <input type="time" v-model="startTime" />
              <span>至</span>
              <input type="time" v-model="endTime" />
            </div>
            <small>目标客户在线高峰时段</small>
          </div>
          <div class="config-item">
            <label>工作日</label>
            <div class="weekday-btns">
              <button v-for="(d, idx) in weekdays" :key="idx"
                class="weekday-btn" :class="{active: workdays.includes(idx)}"
                @click="toggleWeekday(idx)">{{ d }}</button>
            </div>
          </div>
        </div>
      </div>

      <!-- 执行计划预览 -->
      <div class="plan-preview">
        <h4>📊 计划概览</h4>
        <div class="plan-stats">
          <div class="stat-card">
            <div class="stat-num">{{ dailyTarget }}</div>
            <div class="stat-label">每日访问</div>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ dailyTarget * activeDays }}</div>
            <div class="stat-label">每周访问</div>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ dailyTarget * activeDays * 4 }}</div>
            <div class="stat-label">每月预估</div>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ Math.round(dailyTarget * activeDays * 4 * 0.08) }}</div>
            <div class="stat-label">预估回访数</div>
            <small>(~8% 回访率)</small>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ estimatedMinutes }}</div>
            <div class="stat-label">每日耗时(分)</div>
          </div>
        </div>
        <div class="plan-timeline">
          <strong>执行时间线：</strong>
          每次访问停留 {{ visitInterval }}秒，{{ dailyTarget }} 次访问总计约 {{ estimatedMinutes }} 分钟。
          建议{{ workdayNames }}的 {{ startTime }}-{{ endTime }} 执行。
        </div>
      </div>

      <!-- ===== 历史统计仪表盘 ===== -->
      <div class="history-section">
        <h4>📈 访问历史统计</h4>

        <!-- 本周概览 -->
        <div class="week-overview">
          <div class="week-chart">
            <div v-for="(day, idx) in weekData" :key="idx" class="week-bar-col">
              <div class="week-bar-track">
                <div class="week-bar-fill" :style="{height: day.height + '%'}" :class="dayClass(day.count)"></div>
              </div>
              <div class="week-bar-val">{{ day.count }}</div>
              <div class="week-bar-label">{{ day.label }}</div>
            </div>
          </div>
        </div>

        <div class="history-stats">
          <div class="hist-stat">
            <div class="hist-num">{{ totalVisitedAll }}</div>
            <div class="hist-label">累计访问</div>
          </div>
          <div class="hist-stat">
            <div class="hist-num">{{ totalDaysActive }}</div>
            <div class="hist-label">活跃天数</div>
          </div>
          <div class="hist-stat">
            <div class="hist-num">{{ avgDaily }}</div>
            <div class="hist-label">日均访问</div>
          </div>
          <div class="hist-stat">
            <div class="hist-num">{{ streakDays }}</div>
            <div class="hist-label">连续天数</div>
          </div>
        </div>

        <!-- 导出 -->
        <div class="history-actions">
          <button class="btn-export" @click="exportHistory">📥 导出历史记录 (CSV)</button>
          <button class="btn-clear-history" @click="clearHistory">🗑️ 清除历史</button>
        </div>
      </div>

      <!-- 回访信号追踪 -->
      <div class="tracker-section">
        <h4>🔔 回访信号追踪（高意向潜客）</h4>
        <p class="section-desc">当对方回访了你的主页，说明他们产生了好奇心。这类用户是「高意向潜客」，此时人工介入发私信，转化率提升数倍。</p>

        <div class="tracker-input">
          <label>手动记录回访者（粘贴 LinkedIn URL 或姓名）：</label>
          <div class="tracker-row">
            <input v-model="newVisitor" placeholder="如：https://linkedin.com/in/john-smith 或 John Smith" @keyup.enter="addReturnVisitor" />
            <select v-model="visitorIntent">
              <option value="high">🔥 高意向（主动发消息）</option>
              <option value="medium">👆 中意向（查看了多个页面）</option>
              <option value="low">👁️ 低意向（仅浏览主页）</option>
            </select>
            <button class="btn-add-tracker" @click="addReturnVisitor">记录</button>
          </div>
        </div>

        <div v-if="returnVisitors.length" class="tracker-table">
          <table>
            <thead>
              <tr>
                <th>回访者</th>
                <th>意向等级</th>
                <th>回访时间</th>
                <th>建议动作</th>
                <th>状态</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(v, idx) in returnVisitors" :key="idx" :class="v.intent">
                <td><strong>{{ v.name }}</strong></td>
                <td><span class="intent-badge" :class="v.intent">{{ intentLabel(v.intent) }}</span></td>
                <td>{{ v.time }}</td>
                <td class="action-suggest">{{ actionSuggest(v.intent) }}</td>
                <td>
                  <select v-model="v.status" class="status-select">
                    <option value="pending">待跟进</option>
                    <option value="contacted">已联系</option>
                    <option value="replied">已回复</option>
                    <option value="converted">已转化</option>
                    <option value="lost">已流失</option>
                  </select>
                </td>
              </tr>
            </tbody>
          </table>
          <div class="tracker-summary">
            回访总数：<strong>{{ returnVisitors.length }}</strong> |
            高意向：<strong>{{ returnVisitors.filter(v => v.intent === 'high').length }}</strong> |
            已转化：<strong>{{ returnVisitors.filter(v => v.status === 'converted').length }}</strong>
          </div>
        </div>

        <div v-else class="empty-state">
          <p>暂无回访记录。当有人访问你的主页时，在这里记录他们的信息。</p>
          <p class="empty-tip">提示：你可以在 LinkedIn 的「谁查看了你的个人资料」页面查看访客列表。</p>
        </div>
      </div>

      <!-- 转化漏斗 -->
      <div class="funnel-section">
        <h4>📊 转化漏斗追踪</h4>
        <div class="funnel">
          <div class="funnel-step" v-for="(f, idx) in funnelSteps" :key="idx" :style="{width: f.width + '%'}">
            <div class="funnel-label">{{ f.label }}</div>
            <div class="funnel-num">{{ f.count }}</div>
          </div>
        </div>
        <div class="funnel-form">
          <div v-for="(f, idx) in funnelSteps" :key="'f'+idx" class="funnel-input-row">
            <label>{{ f.label }}：</label>
            <input type="number" v-model.number="f.count" min="0" @input="updateFunnel" />
          </div>
        </div>
      </div>

      <!-- 操作建议 -->
      <div class="tip-banner orange">
        <strong>⚠️ 安全提醒：</strong>
        <ul>
          <li>每天访问控制在 <strong>30-50 人</strong>，间隔 60-120 秒，模拟真人浏览行为</li>
          <li>避免使用多账号同时操作同一 IP，容易被 LinkedIn 检测为自动化行为</li>
          <li>每次访问停留 15-30 秒再离开，不要秒进秒出</li>
          <li>回访信号出现后，<strong>24 小时内</strong>人工发私信效果最佳</li>
          <li>定期清理浏览记录（LinkedIn 设置中），避免历史记录过多</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'

const step = ref(1)

// ===== 第一步数据 =====
const bannerChecks = ref([
  { title: '产品/服务相关图片', desc: '不要放风景照，放产品 UI 截图、团队照或品牌相关图', done: false },
  { title: '一句话 Slogan', desc: '如 "AI 驱动的 B2B 获客：效率提升 10 倍"', done: false },
  { title: '联系方式/CTA', desc: '邮箱、网站或"预约通话"按钮', done: false },
])

const headlineChecks = ref([
  { title: '明确身份标签', desc: '如 "B2B Sales Expert" 而非简单的职位名', done: false },
  { title: '价值主张', desc: '你能帮客户解决什么问题', done: false },
  { title: '数据/成果背书', desc: '如 "Helped 200+ companies 3x pipeline"', done: false },
])

const featuredChecks = ref([
  { title: 'Demo 视频 / 产品演示', desc: '30 秒短视频，展示核心功能', done: false },
  { title: '免费体验链接', desc: '降低对方决策成本，引导注册/试用', done: false },
  { title: '案例研究 / 客户见证', desc: '增强信任感', done: false },
])

const aboutChecks = ref([
  { title: '开头 3 行抓眼球', desc: '放上最核心的价值主张和痛点描述', done: false },
  { title: '客户痛点和解决方案', desc: '展示你理解对方的需求', done: false },
  { title: '明确 CTA（行动号召）', desc: '如 "Send me a message" 或 "Book a call"', done: false },
])

const currentHeadline = ref('')
const headlineAnalysis = ref(null)

const categories = computed(() => {
  const bScore = bannerChecks.value.filter(c => c.done).length / Math.max(bannerChecks.value.length, 1) * 25
  const hScore = headlineChecks.value.filter(c => c.done).length / Math.max(headlineChecks.value.length, 1) * 25
  const fScore = featuredChecks.value.filter(c => c.done).length / Math.max(featuredChecks.value.length, 1) * 25
  const aScore = aboutChecks.value.filter(c => c.done).length / Math.max(aboutChecks.value.length, 1) * 25
  return [
    { name: '头图 Banner', icon: '🖼️', score: Math.round(bScore) },
    { name: '标题 Headline', icon: '✏️', score: Math.round(hScore) },
    { name: '置顶 Featured', icon: '📌', score: Math.round(fScore) },
    { name: '简介 About', icon: '📝', score: Math.round(aScore) },
  ]
})

const totalScore = computed(() => categories.value.reduce((s, c) => s + c.score, 0))
const scoreLevel = computed(() => totalScore.value >= 75 ? 'good' : totalScore.value >= 50 ? 'ok' : 'low')

const scoreClass = (score) => score >= 20 ? 'good' : score >= 12 ? 'ok' : 'low'

const calcScore = () => {}

const analyzeHeadline = () => {
  const h = currentHeadline.value.trim()
  if (!h) { headlineAnalysis.value = null; return }

  const issues = []
  const good = []
  const words = h.split(/\s+/).length

  if (words < 5) issues.push('标题太短，建议 5-15 个词')
  else if (words <= 20) good.push('长度适中')
  else issues.push('标题太长，超过 20 个词会被截断')

  if (/\|/.test(h) || /[-–—]/.test(h)) good.push('使用了分隔符增加可读性')
  else issues.push('建议用 "|" 或 "-" 分隔不同信息')

  if (/help/i.test(h) || /enable/i.test(h) || /drive/i.test(h) || /boost/i.test(h)) good.push('包含价值关键词')
  else issues.push('缺少价值关键词（如 help, enable, drive）')

  if (/\d/.test(h)) good.push('包含数字，增加可信度')
  else issues.push('建议加入数据（如 200+, 3x, 10年）')

  const hasPipe = /\|/.test(h)
  if (hasPipe && h.split('|').length >= 2) good.push('多段式标题结构好')

  let level = 'good'
  if (issues.length > good.length) level = issues.length > 3 ? 'low' : 'ok'

  headlineAnalysis.value = {
    level,
    suggestion: good.length > 0 ? `优点：${good.join('；')}。` : '' + (issues.length > 0 ? `建议：${issues.join('；')}。` : ''),
  }
}

// ===== 第二步数据 =====
const targetRoles = ref(['Purchasing Manager', 'CEO', 'Founder'])
const targetIndustries = ref(['Automotive', 'Cross-border Trade'])
const targetRegions = ref(['United States', 'Germany'])
const newRole = ref('')
const newIndustry = ref('')
const newRegion = ref('')
const activityFilter = ref('active_30')
const searchQuery = ref('')

const roleSuggestions = ['Purchasing Manager', 'Procurement Manager', 'Sourcing Manager', 'CEO', 'Founder', 'CTO', 'VP Sales', 'Business Development Manager', 'Supply Chain Director', 'Import Manager']
const industrySuggestions = ['Automotive', 'Cross-border Trade', 'Electronics', 'Machinery', 'Textile', 'Consumer Goods', 'Medical Devices', 'Solar / Renewable Energy', 'Construction', 'Food & Beverage']
const regionSuggestions = ['United States', 'Germany', 'United Kingdom', 'France', 'Japan', 'South Korea', 'Australia', 'Canada', 'UAE', 'Mexico']

const activityOptions = [
  { value: 'active_7', label: '过去 7 天活跃', desc: '最精准，但数量有限' },
  { value: 'active_30', label: '过去 30 天活跃', desc: '推荐平衡点' },
  { value: 'active_90', label: '过去 90 天活跃', desc: '覆盖面广，但部分可能不活跃' },
  { value: 'any', label: '不限活跃度', desc: '覆盖所有用户，需人工判断' },
]

const addRole = () => { if (newRole.value.trim()) { targetRoles.value.push(newRole.value.trim()); newRole.value = '' } }
const addIndustry = () => { if (newIndustry.value.trim()) { targetIndustries.value.push(newIndustry.value.trim()); newIndustry.value = '' } }
const addRegion = () => { if (newRegion.value.trim()) { targetRegions.value.push(newRegion.value.trim()); newRegion.value = '' } }

const generateSearchQuery = () => {
  if (!targetRoles.value.length && !targetIndustries.value.length) {
    alert('请至少添加一个岗位或行业')
    return
  }

  let parts = []

  if (targetRoles.value.length > 0) {
    const roleStr = targetRoles.value.map(r => `"${r}"`).join(' OR ')
    parts.push(`(${roleStr})`)
  }

  if (targetIndustries.value.length > 0) {
    const indStr = targetIndustries.value.map(i => `"${i}"`).join(' OR ')
    parts.push(`(${indStr})`)
  }

  if (targetRegions.value.length > 0) {
    const regStr = targetRegions.value.join(' OR ')
    parts.push(`(${regStr})`)
  }

  const activityMap = {
    active_7: 'Posts in last 7 days',
    active_30: 'Posts in last 30 days',
    active_90: 'Posts in last 90 days',
    any: '',
  }
  if (activityFilter.value !== 'any') {
    parts.push(`FILTER_ACTIVITY("${activityMap[activityFilter.value]}")`)
  }

  searchQuery.value = parts.join(' AND ')
}

const copyText = (text) => {
  navigator.clipboard.writeText(text)
  alert('已复制到剪贴板！')
}

const openLinkedInSearch = () => {
  const roles = targetRoles.value.join(' OR ')
  const industries = targetIndustries.value.join(' OR ')
  const query = `${roles} ${industries}`.trim()
  const url = `https://www.linkedin.com/search/results/people/?keywords=${encodeURIComponent(query)}`
  window.open(url, '_blank')
}

// ===== 第三步：自动化执行器 =====
const STORAGE_KEY = 'lk_visiting_auto'
const HISTORY_KEY = 'lk_visiting_history'
const RETURN_VISITORS_KEY = 'lk_return_visitors'
const FUNNEL_KEY = 'lk_funnel_data'

// 计划配置
const dailyTarget = ref(40)
const visitInterval = ref(90)
const startTime = ref('09:00')
const endTime = ref('17:00')
const workdays = ref([1, 2, 3, 4, 5])
const weekdays = ['一', '二', '三', '四', '五', '六', '日']

const activeDays = computed(() => workdays.value.length)
const estimatedMinutes = computed(() => Math.round(dailyTarget.value * visitInterval.value / 60))
const workdayNames = computed(() => workdays.value.map(i => '周' + weekdays[i]).join('、'))

const toggleWeekday = (idx) => {
  const pos = workdays.value.indexOf(idx)
  if (pos >= 0) workdays.value.splice(pos, 1)
  else workdays.value.push(idx)
  workdays.value.sort()
}

// 队列管理
const todayQueue = ref([])
const importMode = ref('url')
const bulkUrls = ref('')
const manualName = ref('')
const manualUrl = ref('')
const manualRole = ref('')

const todayKey = () => new Date().toISOString().slice(0, 10)

const extractNameFromUrl = (url) => {
  try {
    const u = new URL(url.trim())
    const parts = u.pathname.split('/').filter(Boolean)
    if (parts.length >= 2 && parts[0] === 'in') {
      return parts[1].replace(/-/g, ' ').replace(/\b\w/g, c => c.toUpperCase())
    }
  } catch {}
  return ''
}

const truncateUrl = (url) => {
  if (!url) return ''
  return url.length > 50 ? url.slice(0, 50) + '...' : url
}

const importFromUrls = () => {
  const lines = bulkUrls.value.split('\n').map(l => l.trim()).filter(l => l)
  if (lines.length === 0) { alert('请粘贴至少一个 URL'); return }

  let added = 0
  lines.forEach(line => {
    if (!todayQueue.value.some(p => p.url === line)) {
      todayQueue.value.push({
        name: extractNameFromUrl(line) || '未命名',
        url: line,
        role: '',
        status: 'pending',
        addedAt: Date.now(),
      })
      added++
    }
  })

  bulkUrls.value = ''
  saveTodayQueue()
  alert(`成功导入 ${added} 位潜客到队列（${lines.length - added} 个重复已跳过）`)
}

const addManualProspect = () => {
  if (!manualName.value.trim()) { alert('请输入姓名'); return }
  todayQueue.value.push({
    name: manualName.value.trim(),
    url: manualUrl.value.trim() || '',
    role: manualRole.value || '',
    status: 'pending',
    addedAt: Date.now(),
  })
  manualName.value = ''
  manualUrl.value = ''
  manualRole.value = ''
  saveTodayQueue()
}

const removeFromQueue = (idx) => {
  todayQueue.value.splice(idx, 1)
  saveTodayQueue()
}

const clearQueue = () => {
  if (todayQueue.value.length === 0) return
  if (confirm('确定清空所有队列？已访问的记录不会被删除。')) {
    todayQueue.value = []
    saveTodayQueue()
  }
}

// 自动化执行状态
const autoRunning = ref(false)
const autoPaused = ref(false)
const currentQueueIndex = ref(0)
const countdown = ref(0)
let countdownTimer = null
let sessionStart = null

const currentProspect = computed(() => {
  if (!autoRunning.value && !autoPaused.value) return null
  // Find next pending in queue
  const pending = todayQueue.value.findIndex((p, i) => p.status === 'pending')
  if (pending >= 0) return todayQueue.value[pending]
  return null
})

const visitedToday = computed(() => todayQueue.value.filter(p => p.status === 'visited').length)
const skippedToday = computed(() => todayQueue.value.filter(p => p.status === 'skipped').length)
const progressPercent = computed(() => {
  if (dailyTarget.value === 0) return 0
  return Math.min(Math.round(visitedToday.value / dailyTarget.value * 100), 100)
})

const countdownDisplay = computed(() => {
  const m = Math.floor(countdown.value / 60)
  const s = countdown.value % 60
  return `${m}:${s.toString().padStart(2, '0')}`
})

const countdownOffset = computed(() => {
  if (visitInterval.value === 0) return 0
  const progress = 1 - countdown.value / visitInterval.value
  return 283 * (1 - progress)
})

const sessionDuration = computed(() => {
  if (!sessionStart) return '00:00'
  const elapsed = Math.floor((Date.now() - sessionStart) / 1000)
  const m = Math.floor(elapsed / 60)
  const s = elapsed % 60
  return `${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`
})

const startAutoExecutor = () => {
  if (todayQueue.value.length === 0) { alert('队列中没有潜客，请先导入'); return }

  autoRunning.value = true
  autoPaused.value = false
  sessionStart = Date.now()

  // Mark first pending as current
  const firstPending = todayQueue.value.findIndex(p => p.status === 'pending')
  if (firstPending >= 0) {
    todayQueue.value[firstPending].status = 'current'
    currentQueueIndex.value = firstPending
  }

  startCountdown()
  saveTodayQueue()
}

const startCountdown = () => {
  clearInterval(countdownTimer)
  countdown.value = visitInterval.value

  countdownTimer = setInterval(() => {
    if (autoPaused.value) return
    countdown.value--

    if (countdown.value <= 0) {
      countdown.value = visitInterval.value
      // Play notification sound (visual flash since audio needs user gesture)
      if (Notification.permission === 'granted') {
        new Notification('LinkedIn 访客提醒', {
          body: '时间到！请完成当前浏览后点击「已完成」，准备访问下一位潜客。',
          icon: '🚀'
        })
      }
    }
  }, 1000)
}

const markCurrentDone = () => {
  const current = todayQueue.value.find(p => p.status === 'current')
  if (current) {
    current.status = 'visited'
    logVisit(current)
  }

  // Move to next pending
  const next = todayQueue.value.findIndex(p => p.status === 'pending')
  if (next >= 0 && visitedToday.value < dailyTarget.value) {
    todayQueue.value[next].status = 'current'
    currentQueueIndex.value = next
    countdown.value = visitInterval.value
  } else {
    // All done or reached target
    if (visitedToday.value >= dailyTarget.value) {
      stopAutoExecutor()
      alert(`🎉 今日目标达成！已访问 ${visitedToday.value} 位潜客。`)
    }
  }
  saveTodayQueue()
}

const skipCurrent = () => {
  const current = todayQueue.value.find(p => p.status === 'current')
  if (current) {
    current.status = 'skipped'
  }

  const next = todayQueue.value.findIndex(p => p.status === 'pending')
  if (next >= 0) {
    todayQueue.value[next].status = 'current'
    currentQueueIndex.value = next
    countdown.value = visitInterval.value
  } else {
    stopAutoExecutor()
    alert('队列已清空。')
  }
  saveTodayQueue()
}

const togglePause = () => {
  autoPaused.value = !autoPaused.value
}

const stopAutoExecutor = () => {
  clearInterval(countdownTimer)
  autoRunning.value = false
  autoPaused.value = false

  // Reset any 'current' back to 'pending'
  todayQueue.value.forEach(p => {
    if (p.status === 'current') p.status = 'pending'
  })

  sessionStart = null
  saveTodayQueue()
}

// 历史记录
const visitHistory = ref([])

const logVisit = (prospect) => {
  const entry = {
    name: prospect.name,
    url: prospect.url,
    role: prospect.role,
    timestamp: Date.now(),
    date: todayKey(),
  }
  visitHistory.value.push(entry)
  saveHistory()
}

const totalVisitedAll = computed(() => visitHistory.value.length)
const totalDaysActive = computed(() => new Set(visitHistory.value.map(v => v.date)).size)
const avgDaily = computed(() => {
  if (totalDaysActive.value === 0) return 0
  return Math.round(totalVisitedAll.value / totalDaysActive.value)
})

const streakDays = computed(() => {
  let streak = 0
  const d = new Date()
  while (true) {
    const key = d.toISOString().slice(0, 10)
    if (visitHistory.value.some(v => v.date === key)) {
      streak++
      d.setDate(d.getDate() - 1)
    } else {
      break
    }
  }
  return streak
})

const weekData = computed(() => {
  const dayNames = ['日', '一', '二', '三', '四', '五', '六']
  const result = []
  for (let i = 6; i >= 0; i--) {
    const d = new Date()
    d.setDate(d.getDate() - i)
    const key = d.toISOString().slice(0, 10)
    const count = visitHistory.value.filter(v => v.date === key).length
    result.push({
      label: dayNames[d.getDay()],
      count,
      height: count === 0 ? 4 : Math.min(Math.round(count / dailyTarget.value * 100), 100),
    })
  }
  return result
})

const dayClass = (count) => {
  if (count === 0) return 'zero'
  if (count < dailyTarget.value * 0.5) return 'low'
  if (count < dailyTarget.value) return 'ok'
  return 'good'
}

const exportHistory = () => {
  if (visitHistory.value.length === 0) { alert('暂无历史记录'); return }
  const header = '日期,姓名,URL,岗位,时间\n'
  const rows = visitHistory.value.map(v =>
    `${v.date},"${v.name}","${v.url || ''}","${v.role || ''}",${new Date(v.timestamp).toLocaleString('zh-CN')}`
  ).join('\n')
  const csv = '\uFEFF' + header + rows // BOM for Excel
  const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `linkedin-visiting-history-${todayKey()}.csv`
  a.click()
  URL.revokeObjectURL(url)
}

const clearHistory = () => {
  if (confirm('确定清除所有历史记录？此操作不可恢复。')) {
    visitHistory.value = []
    localStorage.removeItem(HISTORY_KEY)
  }
}

// localStorage 持久化
const saveTodayQueue = () => {
  const data = {
    date: todayKey(),
    queue: todayQueue.value,
    dailyTarget: dailyTarget.value,
    visitInterval: visitInterval.value,
    startTime: startTime.value,
    endTime: endTime.value,
    workdays: workdays.value,
  }
  localStorage.setItem(STORAGE_KEY, JSON.stringify(data))
}

const loadTodayQueue = () => {
  try {
    const raw = localStorage.getItem(STORAGE_KEY)
    if (raw) {
      const data = JSON.parse(raw)
      // Only load if today's data
      if (data.date === todayKey()) {
        todayQueue.value = data.queue || []
        dailyTarget.value = data.dailyTarget || 40
        visitInterval.value = data.visitInterval || 90
        startTime.value = data.startTime || '09:00'
        endTime.value = data.endTime || '17:00'
        workdays.value = data.workdays || [1, 2, 3, 4, 5]
      } else {
        // Old data, reset queue but keep config
        dailyTarget.value = data.dailyTarget || 40
        visitInterval.value = data.visitInterval || 90
        startTime.value = data.startTime || '09:00'
        endTime.value = data.endTime || '17:00'
        workdays.value = data.workdays || [1, 2, 3, 4, 5]
      }
    }
  } catch {}
}

const saveHistory = () => {
  localStorage.setItem(HISTORY_KEY, JSON.stringify(visitHistory.value))
}

const loadHistory = () => {
  try {
    const raw = localStorage.getItem(HISTORY_KEY)
    if (raw) visitHistory.value = JSON.parse(raw)
  } catch {}
}

// 回访追踪
const returnVisitors = ref([])
const newVisitor = ref('')
const visitorIntent = ref('medium')

const intentLabel = (intent) => {
  const map = { high: '🔥 高意向', medium: '👆 中意向', low: '👁️ 低意向' }
  return map[intent] || intent
}

const actionSuggest = (intent) => {
  const map = {
    high: '24h 内发私信 + 预约通话',
    medium: '3天内点赞其动态 → 自然互动',
    low: '持续访问观察，暂不主动联系',
  }
  return map[intent] || ''
}

const addReturnVisitor = () => {
  if (!newVisitor.value.trim()) return
  returnVisitors.value.unshift({
    name: newVisitor.value.trim(),
    intent: visitorIntent.value,
    time: new Date().toLocaleString('zh-CN'),
    status: 'pending',
  })
  newVisitor.value = ''
  localStorage.setItem(RETURN_VISITORS_KEY, JSON.stringify(returnVisitors.value))
}

const loadReturnVisitors = () => {
  try {
    const raw = localStorage.getItem(RETURN_VISITORS_KEY)
    if (raw) returnVisitors.value = JSON.parse(raw)
  } catch {}
}

// 转化漏斗
const funnelSteps = ref([
  { label: '访问潜客主页', count: 0, width: 100 },
  { label: '对方回访你的主页', count: 0, width: 85 },
  { label: '进一步互动（点赞/评论）', count: 0, width: 65 },
  { label: '接受好友请求', count: 0, width: 45 },
  { label: '回复私信', count: 0, width: 30 },
  { label: '达成转化（注册/询盘/签约）', count: 0, width: 18 },
])

const updateFunnel = () => {
  const maxVal = Math.max(...funnelSteps.value.map(f => f.count), 1)
  funnelSteps.value.forEach(f => {
    f.width = Math.max(Math.round(f.count / maxVal * 100), 10)
  })
  localStorage.setItem(FUNNEL_KEY, JSON.stringify(funnelSteps.value))
}

const loadFunnel = () => {
  try {
    const raw = localStorage.getItem(FUNNEL_KEY)
    if (raw) {
      const data = JSON.parse(raw)
      funnelSteps.value = data
    }
  } catch {}
}

// 监听回访者变更持久化
watch(returnVisitors, (val) => {
  localStorage.setItem(RETURN_VISITORS_KEY, JSON.stringify(val))
}, { deep: true })

// 请求通知权限
onMounted(() => {
  loadTodayQueue()
  loadHistory()
  loadReturnVisitors()
  loadFunnel()

  if ('Notification' in window && Notification.permission === 'default') {
    Notification.requestPermission()
  }
})

onUnmounted(() => {
  clearInterval(countdownTimer)
})

// Auto-sync funnel steps[0] count with total visited
watch(totalVisitedAll, (val) => {
  if (funnelSteps.value[0].count < val) {
    funnelSteps.value[0].count = val
    updateFunnel()
  }
})
</script>

<style scoped>
.profile-visiting {
  max-width: 960px;
  margin: 0 auto;
}
h2 {
  color: #0a66c2;
  margin-bottom: 4px;
}
h3 {
  color: #333;
  margin: 24px 0 12px;
}
h4 {
  color: #444;
  margin: 16px 0 10px;
}
.subtitle {
  color: #666;
  margin-bottom: 24px;
  font-size: 14px;
}

/* Step Tabs */
.step-tabs {
  display: flex;
  gap: 4px;
  margin-bottom: 24px;
  background: #f0f0f0;
  border-radius: 12px;
  padding: 4px;
}
.step-tab {
  flex: 1;
  padding: 14px 12px;
  background: none;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  color: #666;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
}
.step-tab.active {
  background: white;
  color: #0a66c2;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}
.step-icon {
  font-size: 16px;
}

/* Info Banners */
.info-banner {
  padding: 16px 20px;
  border-radius: 10px;
  margin-bottom: 24px;
  font-size: 14px;
  line-height: 1.6;
}
.info-banner.blue { background: #e8f0fe; color: #1a56db; border-left: 4px solid #1a56db; }
.info-banner.green { background: #e8f5e9; color: #2e7d32; border-left: 4px solid #2e7d32; }
.info-banner.orange { background: #fff3e0; color: #e65100; border-left: 4px solid #e65100; }

.step-content {
  background: white;
  padding: 24px;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}
.section-desc {
  color: #666;
  font-size: 13px;
  margin-bottom: 16px;
}

/* ===== 第一步：评分 ===== */
.score-overview {
  display: flex;
  gap: 32px;
  align-items: center;
  background: #f8f9fa;
  padding: 24px;
  border-radius: 12px;
  margin-bottom: 24px;
}
.score-circle {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  border: 4px solid #ddd;
}
.score-circle.good { border-color: #2e7d32; background: #e8f5e9; }
.score-circle.ok { border-color: #f57f17; background: #fff8e1; }
.score-circle.low { border-color: #d93025; background: #ffebee; }
.score-num { font-size: 28px; font-weight: 800; line-height: 1; }
.score-label { font-size: 11px; color: #666; margin-top: 2px; }

.score-bars {
  flex: 1;
}
.score-bar-row {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 8px;
}
.bar-label { width: 120px; font-size: 13px; font-weight: 600; flex-shrink: 0; }
.bar-track { flex: 1; height: 8px; background: #e0e0e0; border-radius: 4px; overflow: hidden; }
.bar-fill { height: 100%; border-radius: 4px; transition: width 0.3s; }
.bar-fill.good { background: #2e7d32; }
.bar-fill.ok { background: #f57f17; }
.bar-fill.low { background: #d93025; }
.bar-value { width: 45px; font-size: 13px; font-weight: 600; text-align: right; }

/* Check Items */
.check-section {
  margin-bottom: 20px;
  padding: 16px;
  background: #fafafa;
  border-radius: 10px;
  border: 1px solid #eee;
}
.check-items {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.check-item {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  cursor: pointer;
  padding: 8px;
  border-radius: 6px;
  transition: background 0.2s;
}
.check-item:hover { background: #f0f0f0; }
.check-item input { display: none; }
.check-box {
  width: 22px;
  height: 22px;
  border: 2px solid #ccc;
  border-radius: 6px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
  margin-top: 2px;
}
.check-box.done {
  background: #0a66c2;
  border-color: #0a66c2;
}
.check-box.done::after {
  content: '✓';
  color: white;
  font-size: 14px;
  font-weight: 700;
}
.check-text strong {
  display: block;
  font-size: 14px;
  color: #333;
}
.check-text small {
  display: block;
  font-size: 12px;
  color: #888;
  margin-top: 2px;
}

/* Headline Preview */
.headline-preview {
  margin-top: 12px;
  padding: 12px;
  background: white;
  border-radius: 8px;
  border: 1px solid #e0e0e0;
}
.headline-preview label {
  font-weight: 600;
  font-size: 13px;
  display: block;
  margin-bottom: 6px;
}
.headline-preview textarea {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
  resize: vertical;
  box-sizing: border-box;
  font-family: inherit;
}
.headline-result {
  margin-top: 8px;
  padding: 10px;
  border-radius: 6px;
  font-size: 13px;
  line-height: 1.5;
}
.headline-result.good { background: #e8f5e9; color: #2e7d32; }
.headline-result.ok { background: #fff8e1; color: #f57f17; }
.headline-result.low { background: #ffebee; color: #d93025; }

/* Tip Banner */
.tip-banner {
  margin-top: 20px;
  padding: 16px 20px;
  background: #f0f7ff;
  border-radius: 10px;
  border-left: 4px solid #0a66c2;
  font-size: 13px;
  line-height: 1.8;
}
.tip-banner.orange {
  background: #fff8e1;
  border-left-color: #f57f17;
}
.tip-banner ul {
  margin: 8px 0 0;
  padding-left: 20px;
}
.tip-banner li {
  margin-bottom: 4px;
}

/* ===== 第二步：筛选 ===== */
.filter-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
  margin-bottom: 24px;
}
.filter-card {
  padding: 16px;
  border: 1px solid #e0e0e0;
  border-radius: 10px;
}
.filter-card.highlight {
  border-color: #f57f17;
  background: #fffde7;
}
.filter-card h4 { margin: 0 0 4px; font-size: 14px; }
.filter-desc { font-size: 12px; color: #888; margin-bottom: 10px; }

/* Tags */
.tag-list {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  margin-bottom: 8px;
}
.tag {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  background: #e3f2fd;
  color: #1565c0;
  padding: 4px 10px;
  border-radius: 16px;
  font-size: 12px;
  font-weight: 500;
}
.tag-remove {
  background: none;
  border: none;
  color: #1565c0;
  cursor: pointer;
  font-size: 14px;
  padding: 0 2px;
  opacity: 0.6;
}
.tag-remove:hover { opacity: 1; }
.tag-add {
  display: flex;
  gap: 6px;
}
.tag-add input {
  flex: 1;
  padding: 6px 10px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 12px;
}
.btn-add {
  background: #0a66c2;
  color: white;
  border: none;
  width: 28px;
  height: 28px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 700;
}
.quick-tags {
  margin-top: 8px;
  font-size: 11px;
  color: #999;
}
.quick-tags span { margin-right: 4px; }
.quick-tag {
  background: none;
  border: 1px dashed #ccc;
  color: #666;
  padding: 2px 8px;
  border-radius: 10px;
  font-size: 11px;
  cursor: pointer;
  margin-right: 4px;
}
.quick-tag:hover { border-color: #0a66c2; color: #0a66c2; }

/* Activity Radio */
.activity-filters {
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.radio-item {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  cursor: pointer;
  padding: 6px;
  border-radius: 6px;
}
.radio-item:hover { background: rgba(255,152,0,0.1); }
.radio-item input { display: none; }
.radio-dot {
  width: 18px;
  height: 18px;
  border: 2px solid #ccc;
  border-radius: 50%;
  flex-shrink: 0;
  margin-top: 2px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.radio-item input:checked + .radio-dot {
  border-color: #f57f17;
}
.radio-item input:checked + .radio-dot::after {
  content: '';
  width: 8px;
  height: 8px;
  background: #f57f17;
  border-radius: 50%;
}
.radio-item strong { display: block; font-size: 13px; }
.radio-item small { display: block; font-size: 11px; color: #888; }

/* Search Generator */
.search-generator {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 16px;
}
.btn-generate {
  background: linear-gradient(135deg, #0a66c2, #004182);
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  width: 100%;
  margin-bottom: 12px;
}
.query-result {
  background: white;
  padding: 16px;
  border-radius: 8px;
  border: 1px solid #ddd;
}
.query-text {
  font-family: 'Courier New', monospace;
  background: #1a1a2e;
  color: #00ff88;
  padding: 12px;
  border-radius: 6px;
  font-size: 13px;
  word-break: break-all;
  margin-bottom: 12px;
}
.btn-action {
  background: white;
  border: 1px solid #0a66c2;
  color: #0a66c2;
  padding: 6px 14px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
  margin-right: 8px;
}

/* ===== 第三步：自动化执行器 ===== */
.auto-executor {
  background: linear-gradient(135deg, #f8f9ff, #f0f4ff);
  padding: 24px;
  border-radius: 14px;
  border: 2px solid #d0dcff;
  margin-bottom: 24px;
  transition: all 0.3s;
}
.auto-executor.running {
  border-color: #0a66c2;
  background: linear-gradient(135deg, #f0f7ff, #e3f2fd);
  box-shadow: 0 4px 20px rgba(10,102,194,0.15);
}

.executor-control {
  display: flex;
  gap: 24px;
  align-items: stretch;
}
.control-main {
  flex: 1;
}

.current-target {
  margin-bottom: 16px;
}
.target-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}
.target-counter strong {
  font-size: 24px;
  color: #0a66c2;
}
.target-counter small {
  display: block;
  font-size: 12px;
  color: #888;
}
.target-status {
  font-size: 14px;
  font-weight: 600;
  color: #2e7d32;
}
.target-status.paused {
  color: #f57f17;
}

/* Progress Bar */
.progress-bar-track {
  height: 8px;
  background: #e0e0e0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 6px;
}
.progress-bar-fill {
  height: 100%;
  background: linear-gradient(90deg, #0a66c2, #42a5f5);
  border-radius: 4px;
  transition: width 0.5s ease;
}
.progress-info {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #888;
  margin-bottom: 16px;
}

/* Current Prospect Card */
.current-prospect-card {
  background: white;
  padding: 16px;
  border-radius: 10px;
  border: 2px solid #0a66c2;
  margin-bottom: 16px;
  text-align: center;
}
.prospect-number {
  font-size: 12px;
  color: #888;
  margin-bottom: 4px;
}
.prospect-name {
  font-size: 18px;
  font-weight: 700;
  color: #333;
  margin-bottom: 8px;
}
.visit-link {
  display: inline-block;
  background: #0a66c2;
  color: white;
  padding: 10px 24px;
  border-radius: 8px;
  text-decoration: none;
  font-weight: 600;
  font-size: 14px;
  transition: background 0.2s;
}
.visit-link:hover {
  background: #004182;
}

/* Countdown */
.countdown-area {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
}
.countdown-ring {
  position: relative;
  width: 100px;
  height: 100px;
}
.countdown-ring svg {
  width: 100%;
  height: 100%;
}
.countdown-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 22px;
  font-weight: 800;
  color: #0a66c2;
}
.countdown-label {
  margin-top: 8px;
  font-size: 12px;
  color: #888;
}

/* Idle State */
.idle-state {
  text-align: center;
  padding: 32px 16px;
}
.idle-icon {
  font-size: 48px;
  margin-bottom: 8px;
}
.idle-text {
  color: #888;
  font-size: 14px;
}

/* Control Actions */
.control-actions {
  display: flex;
  flex-direction: column;
  gap: 8px;
  min-width: 200px;
}
.btn-executor {
  padding: 12px 16px;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
  white-space: nowrap;
}
.btn-executor:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
.btn-executor.start {
  background: linear-gradient(135deg, #2e7d32, #43a047);
  color: white;
}
.btn-executor.start:hover:not(:disabled) { background: linear-gradient(135deg, #1b5e20, #2e7d32); }
.btn-executor.done {
  background: linear-gradient(135deg, #0a66c2, #1565c0);
  color: white;
}
.btn-executor.done:hover { background: linear-gradient(135deg, #004182, #0a66c2); }
.btn-executor.skip {
  background: white;
  color: #666;
  border: 1px solid #ddd;
}
.btn-executor.skip:hover { background: #f5f5f5; }
.btn-executor.pause {
  background: white;
  color: #f57f17;
  border: 1px solid #f57f17;
}
.btn-executor.resume {
  background: linear-gradient(135deg, #2e7d32, #43a047);
  color: white;
}
.btn-executor.stop {
  background: white;
  color: #d93025;
  border: 1px solid #d93025;
}
.btn-executor.stop:hover { background: #ffebee; }

/* Daily Mini Stats */
.daily-stats-mini {
  display: flex;
  gap: 16px;
  padding: 12px 16px;
  background: rgba(10,102,194,0.06);
  border-radius: 8px;
  margin-top: 12px;
}
.mini-stat {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
}
.mini-num {
  font-size: 20px;
  font-weight: 800;
  color: #0a66c2;
}
.mini-label {
  font-size: 11px;
  color: #888;
}

/* ===== Queue Management ===== */
.queue-section {
  margin-bottom: 24px;
}
.import-area {
  background: #f8f9fa;
  padding: 16px;
  border-radius: 10px;
  margin-bottom: 16px;
}
.import-tabs {
  display: flex;
  gap: 4px;
  margin-bottom: 12px;
}
.import-tab {
  padding: 8px 16px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
  font-weight: 600;
  color: #666;
  transition: all 0.2s;
}
.import-tab.active {
  background: #0a66c2;
  color: white;
  border-color: #0a66c2;
}
.import-desc {
  font-size: 12px;
  color: #888;
  margin-bottom: 8px;
}
.import-url textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 12px;
  font-family: monospace;
  resize: vertical;
  box-sizing: border-box;
  margin-bottom: 8px;
}
.import-actions {
  display: flex;
  gap: 8px;
}
.btn-import {
  background: linear-gradient(135deg, #0a66c2, #004182);
  color: white;
  border: none;
  padding: 8px 20px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
  font-weight: 600;
}
.btn-clear-queue {
  background: white;
  color: #d93025;
  border: 1px solid #d93025;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
}
.import-manual .manual-row {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}
.manual-row input {
  flex: 1;
  min-width: 120px;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
}
.manual-row select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
}
.btn-add-manual {
  background: #0a66c2;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
}

/* Queue List */
.queue-list {
  border: 1px solid #e0e0e0;
  border-radius: 10px;
  overflow: hidden;
}
.queue-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  background: #f0f0f0;
  font-size: 14px;
}
.queue-count {
  font-size: 12px;
  color: #888;
  font-weight: 400;
}
.queue-items {
  max-height: 300px;
  overflow-y: auto;
}
.queue-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px;
  border-bottom: 1px solid #f0f0f0;
  transition: background 0.2s;
}
.queue-item:hover { background: #f8f9fa; }
.queue-item.visited { opacity: 0.5; }
.queue-item.skipped { opacity: 0.3; text-decoration: line-through; }
.queue-item.current { background: #e3f2fd; border-left: 3px solid #0a66c2; }
.queue-item-left {
  display: flex;
  align-items: center;
  gap: 10px;
  flex: 1;
  min-width: 0;
}
.queue-idx {
  font-size: 12px;
  color: #aaa;
  min-width: 20px;
}
.queue-status-icon {
  font-size: 14px;
}
.queue-item-info {
  min-width: 0;
}
.queue-item-info strong {
  display: block;
  font-size: 13px;
  color: #333;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.queue-item-info small {
  display: block;
  font-size: 11px;
  color: #999;
}
.queue-url {
  font-family: monospace;
  color: #0a66c2 !important;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.queue-item-right {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-shrink: 0;
}
.queue-link {
  font-size: 12px;
  color: #0a66c2;
  text-decoration: none;
  padding: 2px 8px;
  border: 1px solid #0a66c2;
  border-radius: 4px;
}
.queue-link:hover { background: #0a66c2; color: white; }
.queue-remove {
  background: none;
  border: none;
  color: #d93025;
  cursor: pointer;
  font-size: 16px;
  padding: 2px 4px;
  opacity: 0.6;
}
.queue-remove:hover { opacity: 1; }

/* ===== Plan Config ===== */
.plan-config {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 24px;
}
.config-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 16px;
}
.config-item label {
  display: block;
  font-weight: 600;
  font-size: 13px;
  margin-bottom: 6px;
}
.config-item input[type="number"],
.config-item input[type="time"] {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
  box-sizing: border-box;
}
.config-item small {
  display: block;
  font-size: 11px;
  color: #999;
  margin-top: 4px;
}
.time-range {
  display: flex;
  align-items: center;
  gap: 8px;
}
.time-range span { font-size: 13px; color: #666; }
.weekday-btns {
  display: flex;
  gap: 4px;
}
.weekday-btn {
  width: 36px;
  height: 32px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  font-size: 12px;
  font-weight: 600;
  color: #666;
  transition: all 0.2s;
}
.weekday-btn.active {
  background: #0a66c2;
  color: white;
  border-color: #0a66c2;
}

/* Plan Preview */
.plan-preview {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 24px;
}
.plan-stats {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
  flex-wrap: wrap;
}
.stat-card {
  flex: 1;
  min-width: 120px;
  background: white;
  padding: 16px;
  border-radius: 8px;
  text-align: center;
  border: 1px solid #eee;
}
.stat-num {
  font-size: 28px;
  font-weight: 800;
  color: #0a66c2;
  line-height: 1.2;
}
.stat-label {
  font-size: 12px;
  color: #666;
  margin-top: 4px;
}
.stat-card small {
  font-size: 11px;
  color: #999;
}
.plan-timeline {
  font-size: 13px;
  color: #555;
  line-height: 1.6;
  background: white;
  padding: 12px;
  border-radius: 8px;
  border: 1px solid #eee;
}

/* ===== History Section ===== */
.history-section {
  margin-bottom: 24px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 10px;
}

.week-overview {
  margin-bottom: 16px;
}
.week-chart {
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  height: 120px;
  padding: 0 8px;
}
.week-bar-col {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  flex: 1;
}
.week-bar-track {
  width: 32px;
  height: 80px;
  background: #e8e8e8;
  border-radius: 4px;
  display: flex;
  align-items: flex-end;
  overflow: hidden;
}
.week-bar-fill {
  width: 100%;
  border-radius: 4px;
  transition: height 0.5s ease;
  min-height: 4px;
}
.week-bar-fill.good { background: #2e7d32; }
.week-bar-fill.ok { background: #f57f17; }
.week-bar-fill.low { background: #ff9800; }
.week-bar-fill.zero { background: #e0e0e0; }
.week-bar-val {
  font-size: 12px;
  font-weight: 700;
  color: #555;
}
.week-bar-label {
  font-size: 11px;
  color: #888;
}

.history-stats {
  display: flex;
  gap: 16px;
  margin-bottom: 12px;
  flex-wrap: wrap;
}
.hist-stat {
  flex: 1;
  min-width: 80px;
  background: white;
  padding: 12px;
  border-radius: 8px;
  text-align: center;
  border: 1px solid #eee;
}
.hist-num {
  font-size: 22px;
  font-weight: 800;
  color: #0a66c2;
}
.hist-label {
  font-size: 11px;
  color: #888;
}

.history-actions {
  display: flex;
  gap: 8px;
}
.btn-export {
  background: white;
  color: #0a66c2;
  border: 1px solid #0a66c2;
  padding: 6px 14px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
}
.btn-clear-history {
  background: white;
  color: #d93025;
  border: 1px solid #d93025;
  padding: 6px 14px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
}

/* Tracker */
.tracker-section {
  margin-bottom: 24px;
}
.tracker-input {
  background: #f0f7ff;
  padding: 16px;
  border-radius: 10px;
  margin-bottom: 16px;
}
.tracker-input label {
  font-weight: 600;
  font-size: 13px;
  display: block;
  margin-bottom: 8px;
}
.tracker-row {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}
.tracker-row input {
  flex: 1;
  min-width: 200px;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
}
.tracker-row select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 13px;
}
.btn-add-tracker {
  background: #0a66c2;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
  font-weight: 600;
}

/* Tracker Table */
.tracker-table {
  overflow-x: auto;
}
.tracker-table table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}
.tracker-table th {
  background: #f0f0f0;
  padding: 10px 12px;
  text-align: left;
  font-weight: 600;
  font-size: 12px;
  color: #555;
}
.tracker-table td {
  padding: 10px 12px;
  border-bottom: 1px solid #eee;
}
.tracker-table tr:hover { background: #f8f9fa; }
.intent-badge {
  padding: 3px 8px;
  border-radius: 10px;
  font-size: 11px;
  font-weight: 600;
}
.intent-badge.high { background: #ffebee; color: #d93025; }
.intent-badge.medium { background: #fff8e1; color: #f57f17; }
.intent-badge.low { background: #e8f5e9; color: #2e7d32; }
.action-suggest { font-size: 12px; color: #555; }
.status-select {
  padding: 4px 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 12px;
}
.tracker-summary {
  margin-top: 12px;
  padding: 10px;
  background: #f8f9fa;
  border-radius: 6px;
  font-size: 13px;
  color: #555;
}

.empty-state {
  text-align: center;
  padding: 32px;
  color: #999;
}
.empty-state p { margin-bottom: 6px; }
.empty-tip { font-size: 12px; }

/* Funnel */
.funnel-section { margin-bottom: 16px; }
.funnel {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  margin-bottom: 16px;
}
.funnel-step {
  height: 36px;
  background: linear-gradient(135deg, #0a66c2, #1565c0);
  color: white;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: width 0.3s;
}
.funnel-label { font-size: 12px; font-weight: 600; }
.funnel-num { font-size: 16px; font-weight: 800; }
.funnel-form {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 8px;
}
.funnel-input-row {
  display: flex;
  align-items: center;
  gap: 8px;
}
.funnel-input-row label {
  font-size: 12px;
  color: #666;
  white-space: nowrap;
  min-width: 140px;
}
.funnel-input-row input {
  width: 80px;
  padding: 6px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 13px;
}

/* Responsive */
@media (max-width: 768px) {
  .filter-grid { grid-template-columns: 1fr; }
  .score-overview { flex-direction: column; }
  .config-row { grid-template-columns: 1fr 1fr; }
  .plan-stats { flex-direction: column; }
  .funnel-form { grid-template-columns: 1fr; }
  .executor-control { flex-direction: column; }
  .control-actions { flex-direction: row; flex-wrap: wrap; }
  .manual-row { flex-direction: column; }
  .import-manual .manual-row input { min-width: 100%; }
}
</style>
