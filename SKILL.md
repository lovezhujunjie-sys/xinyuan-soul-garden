---
name: 身心灵
description: 开发与迭代「心园」身心灵健康 web 应用（帮全人类交流分享身心灵健康、让人平安喜乐）。单文件 index.html，纯前端 + localStorage，含六大板块：每日心灵卡片、呼吸静心（含 Web Audio 实时合成的雨/海浪/风/颂钵冥想音景）、情绪记录 + 感恩三件好事、养护（拉伸/八段锦/养胃/助眠/四时养生/国学医典）、成长（连续打卡 streak + 活动热力图 + 心情趋势曲线）、社区分享。触发场景：用户想新增/优化心园的功能、改视觉文案、加冥想内容、加拉伸/八段锦/养胃/助眠等养护内容、做数据可视化与打卡、接后端做真实多人社区、或部署分享。每次改完都要：①更新本地 skill 文件 ②commit 并 push 到 GitHub（GitHub Pages 提供在线体验/下载链接）。
---

# 身心灵 · 心园 Wellness App

「心园」是一个身心灵健康应用，愿景是**帮全人类交流分享身心灵健康，让人变得平安喜乐**。本 skill 既是产品代码，也是迭代手册。

## 当前形态

- **单文件** `index.html`，纯前端，无构建步骤、无后端，用浏览器 `localStorage` 存数据。
- 这个文件就是这个 skill 仓库的根，也是 GitHub Pages 的入口（在线体验即下载链接）。

## 六大板块（底部 6 tab）

1. **每日心灵卡片**（首页）— 每天一句正念/感恩语录（按日期确定性选取），可「换一句」。**时辰问候**（`timeGreeting()`，`initDaily` 调用，显示在每日卡顶部 `#greeting`）：按当前小时送一句温柔问候（早安/上午/午间/下午/晚上/夜深六段），让心园像一位会留意你的朋友。**心语珍藏**（`toggleFav()`/`#favBtn` ♡↔♥）：把打动你的句子收藏起来，存 `xy_favquotes`（已纳入 `DB_KEYS` 备份）；「🤍 我珍藏的心语 · N 句」链接（`toggleFavList()`）内联展开珍藏清单（`renderFavList()`），每条可「×」取出（`delFav()`）——难过时回来读一读，是你给自己的光。每次 `initDaily`/`newQuote` 后调 `refreshFav()` 同步收藏态。**分享成图**（`shareQuote()`）：把当前心语画成一张 1080×1080 的疗愈渐变美图（Canvas 实时绘制：紫青暖三色柔光晕 + 自动中文换行的大字 + 出处 + 页脚「心园·愿你平安喜乐 / 愿这份温柔从你心间流向世界 🤍」），优先 `navigator.share` 调起系统分享、否则下载 PNG——让平安喜乐在人与人之间流动，直击初心「帮全人类交流分享」。零依赖、离线可用。语录数组 `QUOTES`（44 条：正念/自我慈悲/感恩/温柔陪伴 + 国学养生金句：黄帝内经、吕氏春秋、嵇康《养生论》、张仲景《金匮要略》、增广贤文等）。<br>**慈心祝福（metta）** `.metta` 卡 + `toggleMetta()`：点「送出一份祝福」后 `#mettaStage` 每 ~5.6s 柔和淡入淡出一句祝福词（`METTA` 数组），由「愿我」→ 所爱的人 → 陌生人 → 全人类 → 一切众生，直击初心「让全人类平安喜乐」。完成一轮 `logActivity('metta')` 计入成长打卡；离开首页或再次点击自动 `stopMetta()`。
2. **呼吸静心**（`view-breath`）— 三种节奏：放松 4-7-8 / 平衡 4-4-4-4 / 安眠 4-6。圆圈随吸气放大、呼气缩小，倒计时 + 计轮数。逻辑 `BREATH_MODES` / `runPhase()`。未开始时圆圈有 7s 缓慢「呼吸」idle 脉动（`@keyframes breatheIdle`，仅 `prefers-reduced-motion:no-preference`）邀请人停下来；结束时按完成轮数给一句温柔收束语（`breathBless(n)`，1-2/3-5/6+ 三档），让平静留在心里。<br>**冥想音景**（`window.Ambient` 模块 + `#ambPills`）：雨/海浪/风/颂钵四种环境音，**全部用 Web Audio 实时合成**（噪声 buffer + BiquadFilter 塑形 / 多泛音 oscillator + LFO 调制），零音频文件、离线可用；同一时刻放一个、平滑交叉淡入淡出，配音量滑块。AudioContext 懒初始化（首次点击 pill 的用户手势内创建并 resume），切走该 tab 时自动 `Ambient.stop()`。
3. **情绪记录 + 感恩三件好事**（`view-mood`）— 10 个情绪 emoji + 文字备注形成「心情足迹」（`MOODS`/`saveMood()`，存 `xy_mood`）；下方**三件好事**每日感恩记录，累积「幸福账本」（`saveGratitude()`/`renderGratLog()`，存 `xy_gratitude`）。**温柔轮换引导语**：`applyGentlePrompts()`（init 调用）按当天日期从 `MOOD_PROMPTS`/`GRAT_PROMPTS` 各取一句，写入心情备注与第一条感恩的 `placeholder`，让空白输入框像一句温柔邀请、帮没头绪的人轻轻起步。**心情/感恩可删除** `delMood(ts)`/`delGrat(ts)`（见第 5 项）。<br>**每一种心情都被回应**：`saveMood()` 保存后按所选 emoji 从 `MOOD_REPLY` 弹一句专属温柔回应（开心→「真为你高兴」、低落→「允许自己慢一点」、疲惫→「好好休息」…），让人觉得「被看见」，而非冷冰冰地存下。**关怀提醒（安全网）**：记录到难熬情绪（😣😔😤，`HARD_MOODS`）时，`maybeShowCrisis()` 在 `#crisisBox` 温柔弹出关怀卡——自我慈悲文案 + 求助热线（12356 全国心理援助 24h / 120 急救 / 110 报警，均为已核实可公开号码，**勿写未经核实的号码**）+ 一键跳「安眠呼吸 4-6」。近 24h 出现 ≥3 次难过会切换更深一层的陪伴语气；记录到正向情绪则自动收起关怀卡。所有热线用 `tel:` 链接。
4. **养护**（`view-care`）— 八个子 tab：拉伸放松 / **八段锦** / 温和养胃 / 助眠安神 / **🖥 护眼办公**（实操类）+ 四时养生 / 黄帝内经 / 医典精萃（国学养生理念）。**护眼办公**（`desk`）专为久坐看屏幕的现代人：20-20-20 护眼法、闭目转眼、掌心熨目、颈椎米字操、手腕舒展、起身微动，6 条均带计时跟练，带就医提示。<br>**动作示范小人**：拉伸 6 式 + 八段锦 8 式每条都带一个**纯内联 SVG 火柴人**示范该体式（`careArtSVG()` / `ART_STD` 标准站姿用 `artFig()` 拼骨架、`ART_RAW` 收录猫牛/坐姿前屈/婴儿式/靠墙抬腿/攀足等特殊体式的手绘 SVG）。零图片、零文件、离线可用；用 CSS 关键帧让小人轻轻「呼吸」+ 个别动作有专属微动效（颈部 `as-neck` 摆头 / 背后七颠 `as-heel` 踮脚 / 托天 `as-press` 上托 / 摇头摆尾 `as-sway` / 往后瞧 `as-twist` / 攒拳 `as-punch` / 猫牛 `as-arch`），均包在 `@media (prefers-reduced-motion:no-preference)` 内，减少动效用户自动定格。`.care-art` 用 `float:left` 让文字环绕。新增动作如需配图,沿用此 SVG 体系、勿外链图片。**八段锦** `baduanjin` 为传统功法八式（健身气功通行版式名），每式带 `sec`/`say` 可单式或「跟练全套」走计时器。子 tab 切换 + 编号清单。**四时养生**出自《黄帝内经·四气调神大论》(春夏秋冬养生),按当前月份用 `currentSeason()` 自动高亮「当季 🌿」徽标,每季带 `food` 节气食疗小贴士(附食疗免责);**黄帝内经/医典精萃**收录《伤寒杂病论》《金匮要略》孙思邈《千金方》等核心养生理念(仅理念非药方,带「不适请就医」提示)。每个国学小节都带 `more` 字段(原文/释义/怎么做),点「展开详解」用 `toggleMore()` 内联展开,逻辑见 `renderCareBody`。数据 `CARE`（纯静态、无用户输入），逻辑 `pickCare()` / `renderCareBody()`；助眠里有按钮 `breathFor(2)` 一键跳到「安眠 4-6」呼吸。养胃带「不适请就医」温柔提示。<br>**跟练计时器**（`#player` overlay）：带 `sec` 的动作有「▶ 跟练」按钮，拉伸还有「跟练全套」序列；倒计时用 RAF 驱动的 SVG 环（可暂停），配 Web Audio 提示音 `chime()` + `speechSynthesis` 语音引导 `speak()`，提示音/语音可开关。**全部零依赖、零文件、离线可用**——这是单文件原则下做"音频/动画/计时"的正确姿势(不嵌真视频)。
5. **成长**（`view-grow`）— 修行打卡 + 数据可视化 + **本周温柔回顾** + **数据备份**。**本周回顾卡**（`.weekly` / `renderWeekly()`）：紧跟 streak 卡，基调「只看见、不评判、不打分」——统计近 7 天的来访天数/呼吸/心情/跟练/感恩/祝福/分享成 `.wk-chip` 徽标，用温柔旁白把这一周说给自己听（最常出现的心情映射为 `MOOD_WARM` 暖词，强调「都被允许、都值得被看见」），结尾随机一句祝福（`blesses`）；零记录时给「此刻就是温柔待己的开始 🌱」的起点文案。**心情/感恩可删除**：`renderMoodLog`/`renderGratLog` 每条带 `.entry-del`（×）按钮，`delMood(ts)`/`delGrat(ts)` 按时间戳过滤、确认后删除并 `renderGrow()` 同步刷新回顾与统计。**数据备份卡**：`exportData()` 把 `xy_mood/posts/gratitude/activity` 打包成 JSON 下载（`心园-我的修行记录-日期.json`），`importData()` 选文件→确认覆盖→`location.reload()` 恢复；守护用户换设备/清缓存时不丢记录（`DB_KEYS`）。卡内含「添加到主屏幕」引导。head 已加 PWA meta（`apple-mobile-web-app-capable`/`theme-color`/`viewport-fit=cover`），手机加到主屏即全屏离线像 App。**连续天数 streak**（火焰卡）、四项总计（呼吸轮数/心情记录/跟练完成/感恩条数）、**近 12 周活动热力图**（GitHub 风格、CSS grid、按当日活动量分级 l1~l4）、**心情趋势曲线**（近 14 次心情按 `MOOD_SCORE` 映射成内联 SVG 面积折线图）。打卡由呼吸完成/心情记录/跟练完成/感恩共同写入：统一走 `logActivity(type,n)` → 存 `xy_activity`（`{ 'YYYY-MM-DD': {breath,mood,care,grat} }`），`computeStreak()`/`activityTotal()` 计算。新增任何"值得鼓励的行为"时，记得调一次 `logActivity()` 让它计入成长。
6. **社区分享**（`view-community`）— 发帖 + 话题标签 + **共鸣(❤️)/祝福(🙏)** + 自己帖子可「轻轻收回」。`likePost()` 共鸣；`blessPost()` 送出/收回祝福并随机温柔回馈语 `BLESS_WORDS`（计数 `bless`/`blessed`）；自己发的帖带 `mine:true`，`delPost()` 确认后删除；发帖 `logActivity('share')` 计入成长。基调是**彼此祝福、共鸣，而非攀比**（「赞」改称「共鸣」）。目前是**本地单机** + 种子数据 `seedPosts()`，存于 `xy_posts`（旧数据向后兼容：`bless||0`、无 `mine` 不显示删除）。要真多人社区需接后端。

## 首次到访欢迎

`#welcome` 全屏温柔欢迎卡：仅首次打开显示（`localStorage` 标记 `xy_welcomed`，`maybeWelcome()` 判断、`closeWelcome()` 关闭并落标记），传达初心与四大板块、强调「记录私密只存本机、离线可用」。Esc 也可关闭（与跟练浮层共用 keydown 处理）。视觉延续疗愈紫青渐变。

## 设计语言

疗愈系深紫夜空背景 + 漂浮光晕（`.orb`）+ 毛玻璃卡片（`backdrop-filter`）。配色变量在 `:root`：`--accent` 紫 / `--accent2` 青 / `--accent3` 暖橙。底部六 tab 导航（每日/呼吸/心情/养护/成长/社区）。视觉与文案都应延续「平安、喜乐、自在、温柔」的基调。**气质收尾细节**：标题「心园」用青→白→紫渐变文字（`background-clip:text`）+ 头顶柔光晕（`header.top::before`）；首页每日卡有 9s 缓慢「呼吸」光晕（`@keyframes dailyBreathe`，仅 `prefers-reduced-motion:no-preference`）；底部导航选中态有上浮放大图标 + 发光小圆点（`.nav button.on::after`）；按钮带 `:active` 轻压回馈；全局柔和滚动条 + 紫色 `::selection` + 字体抗锯齿；卡间距 18px 留白。新增视觉请延续这套"静、柔、有呼吸感"的细节。

## 本地预览

```bash
python3 -m http.server --directory ~/.claude/skills/身心灵 8910
# 浏览器打开 http://localhost:8910/index.html
```

## 迭代铁律（每次修改优化都要做）

> 用户的明确要求：每次改完都执行这两个动作。

1. **更新本地 skill**：直接编辑 `~/.claude/skills/身心灵/index.html`（及本 SKILL.md），这是唯一真源。
2. **推到 GitHub**：在该目录下
   ```bash
   git -C ~/.claude/skills/身心灵 add -A
   git -C ~/.claude/skills/身心灵 commit -m "描述本次优化"
   git -C ~/.claude/skills/身心灵 push
   ```
   推送后 GitHub Pages 自动更新，在线链接即最新版（也是给用户的下载/分享链接）。

## 后续路线（已和用户讨论）

- **a)** 接真后端 → 真实多人社区（账号、发帖、关注、评论）。当前社区仅本地。
- **b)** ✅ 已完成：冥想音景（Web Audio 合成）、心情趋势可视化、每日打卡 streak + 热力图、感恩三件好事、八段锦。后续可继续：冥想引导语音课程、更多功法、数据导出。
- **c)** 改微信小程序便于国内传播。
- **d)** 打磨视觉与文案。
- **关怀提醒**：✅ 已完成 `maybeShowCrisis()`——难过情绪触发关怀卡 + 求助热线，反复难过升级陪伴语气。后续可加更细的危机分级与本地求助资源。

## 注意

- 所有用户输入都已 `escapeHtml()` 转义，新增展示用户内容的地方务必沿用，防 XSS。
- 保持单文件、零依赖的轻量原则，除非用户同意引入后端/框架。
- **无障碍 a11y**：已有全局 `:focus-visible` 焦点环、`@media (prefers-reduced-motion:reduce)` 降低动效、`#breathMeta`/`#plTitle` 的 `aria-live`、跟练浮层 `role="dialog"` + Esc 关闭。新增交互/动效请沿用这些约定。**舒适阅读字号**：标题栏右上「A+」按钮（`#a11yBtn` / `cycleTextScale()`）三档循环（标准/大/特大），用 `zoom`（`.app.scale-1/2` + `.nav.scale-1/2`）整体等比放大、留白同步放大，存 `xy_textscale`，`applyTextScale()` 在 init 时恢复——守护看屏吃力的长辈与低视力用户（直击「全人类温柔可及」）。
- **跟练计时器**用 `PL.adv`（setTimeout id）+ `PL.raf` 防止快速「下一个」时定时器叠加；切走呼吸 tab 时 `go()` 会调 `stopBreath()` 防止后台计时泄漏。
- **医疗免责**：拉伸/八段锦/养胃/助眠/养生等养护内容均带 `note` 字段温柔提示「不适请就医、特殊人群先咨询医生」，新增养护内容请一并加 `note`。
