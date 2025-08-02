<script setup>
import { ref, onMounted, onUnmounted, nextTick, watch } from 'vue'

// 页面导航
const currentPage = ref(1)
const totalPages = 5
const isScrolling = ref(false)
const scrollStartY = ref(0)
const scrollCurrentY = ref(0)

// 公会创建时间
const guildCreatedTime = new Date('2022-08-09 11:51:44')
const preciseYears = ref(0)
const displayYears = ref('0.00000000')
const memberCount = ref(125)
const guildLevel = ref(91)
const supportEmail = ref('support@juzi.club')
const qqGroup = ref('884588499')

// 多语言支持
  const currentLanguage = ref('zh')
  const detectedLanguage = ref('')
  const showLanguageNotification = ref(false)
  
  // 问候系统
  const showGreeting = ref(false)
  const greetingMessage = ref('')
  
  
  const translations = {
  zh: {
    guildName: 'JuziClub',
    baseText: '一个',
    suffixText: '公会',
    memberCount: '成员',
    scrollHint: '滑动',
    establishedText: '公会成立至今',
    yearsText: '年',
    daysText: '天',
    hoursText: '小时',
    minutesText: '分钟',
    secondsText: '秒',
    guildLevelText: '公会等级',
    contactUsText: '联系我们',
    emailText: '邮箱',
    qqGroupText: 'QQ群'
  },
  en: {
    guildName: 'JuziClub',
    baseText: 'A',
    suffixText: 'Guild',
    memberCount: 'Members',
    scrollHint: 'Scroll',
    establishedText: 'Guild Established',
    yearsText: 'Years',
    daysText: 'Days',
    hoursText: 'Hours',
    minutesText: 'Minutes',
    secondsText: 'Seconds',
    guildLevelText: 'Guild Level',
    contactUsText: 'Contact Us',
    emailText: 'Email',
    qqGroupText: 'QQ Group'
  },
  ja: {
    guildName: 'JuziClub',
    baseText: 'ひとつの',
    suffixText: 'ギルド',
    memberCount: 'メンバー',
    scrollHint: 'スクロール',
    establishedText: 'ギルド設立から',
    yearsText: '年',
    daysText: '日',
    hoursText: '時間',
    minutesText: '分',
    secondsText: '秒',
    guildLevelText: 'ギルドレベル',
    contactUsText: 'お問い合わせ',
    emailText: 'メール',
    qqGroupText: 'QQグループ'
  },
  ko: {
    guildName: 'JuziClub',
    baseText: '하나의',
    suffixText: '길드',
    memberCount: '멤버',
    scrollHint: '스크롤',
    establishedText: '길드 설립 이후',
    yearsText: '년',
    daysText: '일',
    hoursText: '시간',
    minutesText: '분',
    secondsText: '초',
    guildLevelText: '길드 레벨',
    contactUsText: '문의하기',
    emailText: '이메일',
    qqGroupText: 'QQ그룹'
  }
}

// 翻译函数
const t = (key) => {
  return translations[currentLanguage.value][key] || key
}

// 检测语言
const detectLanguage = () => {
  const browserLang = navigator.language || navigator.userLanguage
  let detected = 'zh'
  if (browserLang.startsWith('en')) {
    detected = 'en'
  } else if (browserLang.startsWith('ja')) {
    detected = 'ja'
  } else if (browserLang.startsWith('ko')) {
    detected = 'ko'
  }
  
  detectedLanguage.value = detected
  if (detected !== 'zh') {
    showLanguageNotification.value = true
    setTimeout(() => {
      showLanguageNotification.value = false
    }, 5000)
  } else {
    currentLanguage.value = detected
  }
}

// 切换语言
const switchLanguage = (lang) => {
  currentLanguage.value = lang
  showLanguageNotification.value = false
}

// 页面切换函数
const nextPage = () => {
  if (currentPage.value < totalPages) {
    const newPage = currentPage.value + 1
    animatePageChange(newPage)
    currentPage.value = newPage
  }
}

const prevPage = () => {
  if (currentPage.value > 1) {
    const newPage = currentPage.value - 1
    animatePageChange(newPage)
    currentPage.value = newPage
  }
}

// 返回第一页
const goToFirstPage = () => {
  if (isScrolling.value) return
  
  isScrolling.value = true
  animatePageChange(1)
  currentPage.value = 1
  
  setTimeout(() => {
    isScrolling.value = false
  }, 800)
}

// 获取语言名称
const getLanguageName = (lang) => {
  const names = {
    'zh': '中文',
    'en': 'English',
    'ja': '日本語',
    'ko': '한국어'
  }
  return names[lang] || '中文'
}

// 数字切换动画函数
const animateYearsChange = (newValue) => {
  const oldValue = displayYears.value
  const startTime = Date.now()
  const duration = 300 // 动画持续时间
  
  const animate = () => {
    const elapsed = Date.now() - startTime
    const progress = Math.min(elapsed / duration, 1)
    
    // 使用缓动函数
    const easeProgress = 1 - Math.pow(1 - progress, 3)
    
    // 插值计算当前显示值
    const currentValue = parseFloat(oldValue) + (newValue - parseFloat(oldValue)) * easeProgress
    displayYears.value = currentValue.toFixed(8)
    
    if (progress < 1) {
      requestAnimationFrame(animate)
    } else {
      displayYears.value = newValue.toFixed(8)
    }
  }
  
  requestAnimationFrame(animate)
}

// 页码数字动画
const displayCurrentPage = ref(1)
const isPageAnimating = ref(false)

const animatePageChange = (newPage) => {
  if (isPageAnimating.value || displayCurrentPage.value === newPage) return
  
  isPageAnimating.value = true
  const oldPage = displayCurrentPage.value
  const startTime = Date.now()
  const duration = 300 // 动画持续时间
  
  const animate = () => {
    const elapsed = Date.now() - startTime
    const progress = Math.min(elapsed / duration, 1)
    
    // 使用缓动函数
    const easeProgress = 1 - Math.pow(1 - progress, 3)
    
    // 插值计算当前显示值
    const currentValue = oldPage + (newPage - oldPage) * easeProgress
    displayCurrentPage.value = Math.round(currentValue)
    
    if (progress < 1) {
      requestAnimationFrame(animate)
    } else {
      displayCurrentPage.value = newPage
      isPageAnimating.value = false
    }
  }
  
  requestAnimationFrame(animate)
}

// 监听currentPage变化，同步更新displayCurrentPage
watch(currentPage, (newPage) => {
  animatePageChange(newPage)
}, { immediate: true })

// 计算精确年份 (使用UTC+8时区)
const calculatePreciseYears = () => {
  const now = new Date()
  // 转换为UTC+8时区
  const utc8Now = new Date(now.getTime() + (8 * 60 * 60 * 1000))
  const utc8Creation = new Date(guildCreatedTime.getTime() + (8 * 60 * 60 * 1000))
  const diff = utc8Now - utc8Creation
  
  // 计算精确到毫秒的年份
  const millisecondsPerYear = 1000 * 60 * 60 * 24 * 365.25 // 考虑闰年
  const newPreciseYears = diff / millisecondsPerYear
  
  // 始终触发动画更新
  if (displayYears.value === '0.00000000') {
    // 首次初始化时直接设置值
    displayYears.value = newPreciseYears.toFixed(8)
  } else {
    // 每次都触发动画
    animateYearsChange(newPreciseYears)
  }
  
  preciseYears.value = newPreciseYears
}



// 滚动切换页面
const handleTouchStart = (e) => {
  scrollStartY.value = e.touches[0].clientY
  isScrolling.value = false
}

const handleTouchMove = (e) => {
  if (isScrolling.value) return
  scrollCurrentY.value = e.touches[0].clientY
}

const handleTouchEnd = () => {
  if (isScrolling.value) return
  const deltaY = scrollStartY.value - scrollCurrentY.value
  const threshold = 50
  
  if (Math.abs(deltaY) > threshold) {
    isScrolling.value = true
    if (deltaY > 0 && currentPage.value < totalPages) {
      const newPage = currentPage.value + 1
      animatePageChange(newPage)
      currentPage.value = newPage
    } else if (deltaY < 0 && currentPage.value > 1) {
      const newPage = currentPage.value - 1
      animatePageChange(newPage)
      currentPage.value = newPage
    }
    setTimeout(() => {
      isScrolling.value = false
    }, 800)
  }
}

const handleWheel = (e) => {
  if (isScrolling.value) return
  e.preventDefault()
  
  isScrolling.value = true
  if (e.deltaY > 0 && currentPage.value < totalPages) {
    const newPage = currentPage.value + 1
    animatePageChange(newPage)
    currentPage.value = newPage
  } else if (e.deltaY < 0 && currentPage.value > 1) {
    const newPage = currentPage.value - 1
    animatePageChange(newPage)
    currentPage.value = newPage
  }
  
  setTimeout(() => {
    isScrolling.value = false
  }, 800)
}

// 动画效果相关
const currentGameText = ref('')
const displayGameText = ref('')
const currentGameIndex = ref(0)
const games = ['Hypixel', 'MC', 'CS2']
const baseText = '一个'
const suffixText = '公会'
const isAnimating = ref(false)
const isTyping = ref(false)
const isErasing = ref(false)
const showCursor = ref(true)

// 逐字显示和消失动画
const animateGame = async () => {
  isAnimating.value = true
  const currentGame = games[currentGameIndex.value]
  currentGameText.value = currentGame
  
  // 从左到右逐字显示
  isTyping.value = true
  displayGameText.value = ''
  for (let i = 0; i <= currentGame.length; i++) {
    displayGameText.value = currentGame.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 120))
  }
  isTyping.value = false
  
  // 停留一段时间
  await new Promise(resolve => setTimeout(resolve, 2500))
  
  // 从右到左逐字消失
  isErasing.value = true
  for (let i = currentGame.length; i >= 0; i--) {
    displayGameText.value = currentGame.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 80))
  }
  isErasing.value = false
  
  currentGameText.value = ''
  isAnimating.value = false
  currentGameIndex.value = (currentGameIndex.value + 1) % games.length
  
  // 短暂停顿后开始下一个
  await new Promise(resolve => setTimeout(resolve, 600))
  animateGame()
}

// 光标闪烁
const startCursorBlink = () => {
  setInterval(() => {
    showCursor.value = !showCursor.value
  }, 500)
}

// 问候系统函数
const showGreetingMessage = () => {
  const hour = new Date().getHours()
  let greeting = ''
  
  if (hour >= 5 && hour < 12) {
    greeting = '早上好！欢迎来到JuziClub官网'
  } else if (hour >= 12 && hour < 18) {
    greeting = '下午好！欢迎来到JuziClub官网'
  } else {
    greeting = '晚上好！欢迎来到JuziClub官网'
  }
  
  greetingMessage.value = greeting
  
  showGreeting.value = true
  // 2秒后开始隐藏动画
  setTimeout(() => {
    showGreeting.value = false
  }, 2000)
}



let timer = null

onMounted(async () => {
  detectLanguage()
  calculatePreciseYears()
  timer = setInterval(calculatePreciseYears, 100) // 更频繁的更新以显示精确变化
  
  // 延迟0.3秒显示问候消息
  setTimeout(() => {
    showGreetingMessage()
  }, 300)
  
  // 延迟启动动画效果
  await nextTick()
  setTimeout(() => {
    animateGame()
    startCursorBlink()
  }, 1500)
})

onUnmounted(() => {
  if (timer) {
    clearInterval(timer)
  }
})
</script>

<template>
  <div class="app" @wheel="handleWheel" @touchstart="handleTouchStart" @touchmove="handleTouchMove" @touchend="handleTouchEnd">
    <div class="background">
      <div class="gradient-orb orb-1"></div>
      <div class="gradient-orb orb-2"></div>
      <div class="gradient-orb orb-3"></div>
      <div class="meteors">
        <div class="meteor meteor-1"></div>
      </div>
    </div>

    <div class="page page-1" :class="{ active: currentPage === 1, 'slide-out-up': currentPage > 1 }">
      <div class="page-content">
        <div class="guild-header">
          <h1 class="guild-name">JuziClub</h1>
        </div>

        <div class="slogan-container">
          <div class="slogan-text">
            <span class="base-text" :class="{ 'text-adjust': isAnimating }">{{ t('baseText') }}</span>
            <span class="game-text" :class="{ 'typing': isTyping, 'erasing': isErasing }">{{ displayGameText }}</span>
            <span class="suffix-text" :class="{ 'text-adjust': isAnimating }">{{ t('suffixText') }}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="page page-2" :class="{ active: currentPage === 2, 'slide-out-up': currentPage > 2, 'slide-out-down': currentPage < 2 }">
      <div class="page-content">
        <div class="time-counter">
          <h3 class="counter-title">{{ t('establishedText') }}</h3>
          <div class="precise-years-display">
            <div class="years-container">
              <span class="precise-years-number">{{ displayYears }}</span>
              <span class="years-label">{{ t('yearsText') }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="page page-3" :class="{ active: currentPage === 3, 'slide-out-up': currentPage > 3, 'slide-out-down': currentPage < 3 }">
      <div class="page-content">
        <div class="member-count">
          <div class="member-info">
            <span class="member-number">{{ memberCount }}</span>
            <span class="member-label">{{ t('memberCount') }}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="page page-4" :class="{ active: currentPage === 4, 'slide-out-up': currentPage > 4, 'slide-out-down': currentPage < 4 }">
      <div class="page-content">
        <div class="guild-level">
          <div class="level-info">
            <span class="level-number">{{ guildLevel }}</span>
            <span class="level-label">{{ t('guildLevelText') }}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="page page-5" :class="{ active: currentPage === 5, 'slide-out-down': currentPage < 5 }">
      <div class="page-content">
        <div class="contact-us">
          <div class="contact-title">{{ t('contactUsText') }}</div>
          <div class="contact-info">
             <div class="contact-item">
               <div class="contact-label">{{ t('emailText') }}</div>
               <a :href="'mailto:' + supportEmail" class="contact-value email">{{ supportEmail }}</a>
             </div>
             <div class="contact-item">
               <div class="contact-label">{{ t('qqGroupText') }}</div>
               <a href="https://qm.qq.com/q/uQg9xeUZ3i" target="_blank" class="contact-value qq-group">{{ qqGroup }}</a>
             </div>
           </div>
        </div>
      </div>
    </div>

    <div class="scroll-arrow" v-if="currentPage < totalPages">
      <div class="arrow-icon"></div>
      <div class="arrow-text">{{ t('scrollHint') }}</div>
    </div>
    
     <div class="mini-title" v-if="currentPage !== 1" @click="goToFirstPage">
       <span class="mini-guild-name">{{ t('guildName') }}</span>
     </div>
     
     <div class="page-indicator" v-if="currentPage !== 1">
        <span class="page-number" :class="{ changing: isPageAnimating }">{{ displayCurrentPage }}</span>
        <span class="page-separator">/</span>
        <span class="total-pages">{{ totalPages }}</span>
      </div>
    
    <div class="language-notification" v-if="showLanguageNotification">
      <div class="notification-content">
        <p class="notification-text">检测到您的语言为 {{ getLanguageName(detectedLanguage) }}</p>
        <div class="notification-buttons">
          <button @click="switchLanguage(detectedLanguage)" class="switch-btn">切换</button>
          <button @click="showLanguageNotification = false" class="keep-btn">保持中文</button>
        </div>
      </div>
    </div>
    
    <Transition name="greeting" appear>
      <div v-if="showGreeting" class="greeting-bubble">
        <div class="greeting-content">
          <p class="greeting-text">{{ greetingMessage }}</p>
        </div>
      </div>
    </Transition>
    
    <footer class="copyright">
      <p>&copy; 2025 JuziClub. All rights reserved.</p>
    </footer>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@100;200;300;400;500;600;700;800;900&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* 隐藏滚动条 */
::-webkit-scrollbar {
  display: none;
}

html {
  -ms-overflow-style: none;
  scrollbar-width: none;
  overflow-y: hidden;
}

body {
  overflow-y: hidden;
}

/* 隐藏所有滚动条 */
* {
  -ms-overflow-style: none !important;
  scrollbar-width: none !important;
}

*::-webkit-scrollbar {
  display: none !important;
  width: 0 !important;
  height: 0 !important;
}

html, body {
  overflow-x: hidden !important;
  overflow-y: hidden !important;
}

#app {
  overflow: hidden !important;
}

.app {
  font-family: 'Inter', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  line-height: 1.6;
  color: #fff;
  overflow-x: hidden;
  overflow-y: hidden;
  min-height: 100vh;
  position: relative;
}

/* 动态背景 */
.background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #ffc0cb 0%, #ffb6c1 25%, #ffa0b4 50%, #ff91a4 75%, #ff8fa3 100%);
  z-index: -1;
  animation: backgroundPulse 6s ease-in-out infinite;
  filter: blur(0.5px);
}

.background::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at 50% 50%, rgba(255, 255, 255, 0.1) 0%, transparent 50%);
  animation: dynamicBlur 8s ease-in-out infinite;
  filter: blur(1px);
}

.gradient-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(60px);
  opacity: 0.7;
  animation: float 8s ease-in-out infinite, orbBlur 10s ease-in-out infinite;
  transition: filter 0.3s ease;
}

.orb-1 {
  width: 300px;
  height: 300px;
  background: radial-gradient(circle, rgba(255, 192, 203, 0.3) 0%, transparent 70%);
  top: 10%;
  left: 10%;
  animation-delay: 0s;
}

.orb-2 {
  width: 400px;
  height: 400px;
  background: radial-gradient(circle, rgba(255, 182, 193, 0.25) 0%, transparent 70%);
  top: 50%;
  right: 10%;
  animation-delay: 2s;
}

.orb-3 {
  width: 250px;
  height: 250px;
  background: radial-gradient(circle, rgba(255, 160, 180, 0.2) 0%, transparent 70%);
  bottom: 20%;
  left: 30%;
  animation-delay: 4s;
}

@keyframes float {
  0%, 100% {
    transform: translate(0, 0) scale(1);
  }
  33% {
    transform: translate(30px, -30px) scale(1.1);
  }
  66% {
    transform: translate(-20px, 20px) scale(0.9);
  }
}

@keyframes backgroundPulse {
  0%, 100% {
    filter: blur(0.5px) brightness(1);
  }
  50% {
    filter: blur(1px) brightness(1.05);
  }
}

@keyframes dynamicBlur {
  0%, 100% {
    filter: blur(1px);
    opacity: 0.3;
  }
  25% {
    filter: blur(2px);
    opacity: 0.5;
  }
  50% {
    filter: blur(0.5px);
    opacity: 0.2;
  }
  75% {
    filter: blur(1.5px);
    opacity: 0.4;
  }
}

@keyframes orbBlur {
  0%, 100% {
    filter: blur(60px);
  }
  25% {
    filter: blur(65px);
  }
  50% {
    filter: blur(55px);
  }
  75% {
    filter: blur(70px);
  }
}

@keyframes pageActivate {
  0% {
    filter: blur(5px);
    opacity: 0;
  }
  50% {
    filter: blur(2px);
    opacity: 0.7;
  }
  100% {
    filter: blur(0px);
    opacity: 1;
  }
}



/* 页面布局 */
.page {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
  z-index: 1;
  opacity: 0;
  transform: translateY(100vh);
  transition: all 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  filter: blur(0px);
}

.page.active {
  opacity: 1;
  transform: translateY(0);
  filter: blur(0px);
  animation: pageActivate 0.8s ease-out;
}

.page.slide-out-up {
  opacity: 0;
  transform: translateY(-100vh);
  filter: blur(3px);
}

.page.slide-out-down {
  opacity: 0;
  transform: translateY(100vh);
  filter: blur(3px);
}

.page-content {
  text-align: center;
  max-width: 900px;
  width: 100%;
}

/* 公会标题 */
.guild-header {
  margin-bottom: 3rem;
  animation: fadeInDown 1s ease-out;
}

.guild-name {
  font-size: 8rem;
  font-weight: 500;
  background: linear-gradient(45deg, 
    #ff6b35, 
    #ff8c42, 
    #ffa726, 
    #ffb74d, 
    #ffc107, 
    #ffca28, 
    #ffd54f, 
    #ffe082, 
    #ffecb3, 
    #ffe082, 
    #ffd54f, 
    #ff6b35
  );
  background-size: 300% 300%;
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 0 0 20px rgba(255, 107, 53, 0.8), 0 0 40px rgba(255, 140, 66, 0.6), 0 0 60px rgba(255, 167, 38, 0.4), 0 0 80px rgba(255, 183, 77, 0.2);
  margin-bottom: 3rem;
  position: relative;
  z-index: 2;
  letter-spacing: -0.01em;
  filter: drop-shadow(0 0 30px rgba(255, 140, 66, 0.5));
}





@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 打字效果标语 */
.slogan-container {
  margin-bottom: 3rem;
  animation: fadeInUp 1s ease-out 0.6s both;
}

.slogan-text {
  font-size: 2.2rem;
  color: #ffffff;
  font-weight: 600;
  text-shadow: 0 0 25px rgba(255, 192, 203, 0.8), 0 2px 4px rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.3rem;
  min-height: 3rem;
  position: relative;
  animation: subtleBlur 5s ease-in-out infinite;
  filter: blur(0px);
  transition: filter 0.3s ease;
}

.base-text, .suffix-text {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  white-space: nowrap;
}

.base-text.text-adjust, .suffix-text.text-adjust {
  transform: translateX(0);
}

.game-text {
  background: linear-gradient(90deg, #ff1493 0%, #ff69b4 25%, #ff91c7 50%, #ff1493 75%, #ff69b4 100%);
  background-size: 300% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  display: inline-block;
  position: relative;
  filter: drop-shadow(0 0 20px rgba(255, 20, 147, 1));
  min-width: 20px;
  text-align: left;
  white-space: nowrap;
  transition: all 0.3s ease;
  animation: gradientMove 3s ease-in-out infinite;
  backdrop-filter: blur(10px);
  padding: 0.2em 0.4em;
  border-radius: 8px;
  border: 1px solid rgba(255, 20, 147, 0.5);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.game-text.typing {
  animation: gradientMove 3s ease-in-out infinite, textGlow 1s ease-in-out infinite;
}

.game-text.erasing {
  animation: gradientMove 3s ease-in-out infinite, textFade 0.5s ease-in-out infinite;
}



@keyframes textGlow {
  0%, 100% {
    filter: drop-shadow(0 0 15px rgba(255, 192, 203, 0.8));
  }
  50% {
    filter: drop-shadow(0 0 25px rgba(255, 192, 203, 1));
  }
}

@keyframes textFade {
  0%, 100% {
    filter: drop-shadow(0 0 15px rgba(255, 192, 203, 0.8));
  }
  50% {
    filter: drop-shadow(0 0 10px rgba(255, 192, 203, 0.6));
  }
}

@keyframes gradientMove {
  0%, 100% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
}

/* 成员数量显示 */
.member-count {
  margin-top: 4rem;
  animation: fadeInUp 1s ease-out 1.2s both;
}

.member-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.member-number {
  font-size: 4rem;
  font-weight: 700;
  color: #ffffff;
  text-shadow: 0 0 25px rgba(255, 192, 203, 0.9), 0 0 50px rgba(255, 105, 180, 0.7), 0 2px 4px rgba(0, 0, 0, 0.3);
  animation: memberGlow 3s ease-in-out infinite;
}

.member-label {
  font-size: 1.8rem;
  color: #ffffff;
  font-weight: 500;
  text-shadow: 0 0 20px rgba(255, 192, 203, 0.8), 0 2px 4px rgba(0, 0, 0, 0.3);
}

@keyframes memberGlow {
  0%, 100% {
    text-shadow: 0 0 25px rgba(255, 192, 203, 0.9), 0 0 50px rgba(255, 105, 180, 0.7), 0 2px 4px rgba(0, 0, 0, 0.3);
  }
  50% {
    text-shadow: 0 0 35px rgba(255, 192, 203, 1), 0 0 70px rgba(255, 105, 180, 0.9), 0 2px 4px rgba(0, 0, 0, 0.3);
  }
}

/* 公会等级显示 */
.guild-level {
  margin-top: 4rem;
  animation: fadeInUp 1s ease-out 1.2s both;
}

.level-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.level-number {
  font-size: 4rem;
  font-weight: 700;
  color: #ffffff;
  text-shadow: 0 0 25px rgba(255, 192, 203, 0.9), 0 0 50px rgba(255, 105, 180, 0.7), 0 2px 4px rgba(0, 0, 0, 0.3);
  animation: levelGlow 3s ease-in-out infinite;
}

.level-label {
  font-size: 1.8rem;
  color: #ffffff;
  font-weight: 500;
  text-shadow: 0 0 20px rgba(255, 192, 203, 0.8), 0 2px 4px rgba(0, 0, 0, 0.3);
}

@keyframes levelGlow {
  0%, 100% {
    text-shadow: 0 0 25px rgba(255, 192, 203, 0.9), 0 0 50px rgba(255, 105, 180, 0.7), 0 2px 4px rgba(0, 0, 0, 0.3);
  }
  50% {
    text-shadow: 0 0 35px rgba(255, 192, 203, 1), 0 0 70px rgba(255, 105, 180, 0.9), 0 2px 4px rgba(0, 0, 0, 0.3);
  }
}

  /* 联系我们显示 */
  .contact-us {
    animation: fadeInUp 1s ease-out 0.3s both;
  }

  .contact-title {
    font-size: 2.5rem;
    font-weight: 700;
    color: #ffffff;
    text-shadow: 0 0 30px rgba(255, 192, 203, 1), 0 0 60px rgba(255, 105, 180, 0.8), 0 4px 8px rgba(0, 0, 0, 0.3);
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    text-align: center;
    margin-bottom: 2rem;
    transition: transform 0.3s ease;
  }

  .contact-title:hover {
    transform: scale(1.05);
  }

  .contact-info {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
    align-items: center;
  }

  .contact-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5rem;
  }

  .contact-label {
    font-size: 1.2rem;
    color: #ffffff;
    font-weight: 500;
    text-shadow: 0 0 15px rgba(255, 192, 203, 0.6), 0 2px 4px rgba(0, 0, 0, 0.3);
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  }

  .contact-value {
    font-size: 1.6rem;
    font-weight: 600;
    color: #ffffff;
    text-shadow: 0 0 25px rgba(255, 192, 203, 0.9), 0 0 50px rgba(255, 105, 180, 0.7), 0 4px 8px rgba(0, 0, 0, 0.3);
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    transition: transform 0.3s ease, text-shadow 0.3s ease;
    cursor: pointer;
    text-decoration: none;
  }

  .contact-value:visited {
    color: #ffffff;
  }

  .contact-value:hover {
    transform: scale(1.1);
    text-shadow: 0 0 35px rgba(255, 192, 203, 1), 0 0 70px rgba(255, 105, 180, 0.9), 0 4px 8px rgba(0, 0, 0, 0.3);
  }

  .email {
    color: #ffffff;
  }

  .qq-group {
    color: #ffffff;
  }

/* 时间计算器 */
.time-counter {
  animation: fadeInUp 1s ease-out 0.3s both;
}

.counter-title {
  font-size: 2.2rem;
  color: #ffffff;
  margin-bottom: 2rem;
  font-weight: 500;
  text-shadow: 0 0 25px rgba(255, 192, 203, 0.8), 0 2px 4px rgba(0, 0, 0, 0.4);
  display: inline-block;
}

.precise-years-display {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.years-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: all 0.3s ease;
}

.precise-years-number {
  font-size: 3.5rem;
  font-weight: 700;
  color: #ffffff;
  text-shadow: 0 0 30px rgba(255, 192, 203, 1), 0 0 60px rgba(255, 105, 180, 0.8), 0 4px 8px rgba(0, 0, 0, 0.3);
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  letter-spacing: 0.02em;
  transition: all 0.3s ease;
  animation: numberGlow 2s ease-in-out infinite alternate, numberBlur 4s ease-in-out infinite;
  font-feature-settings: 'tnum' 1;
  filter: blur(0px);
}

.years-label {
  font-size: 1.3rem;
  color: #ffffff;
  margin-top: 0.5rem;
  font-weight: 500;
  text-shadow: 0 0 20px rgba(255, 192, 203, 0.8), 0 2px 4px rgba(0, 0, 0, 0.3);
  letter-spacing: 0.1em;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

@keyframes numberGlow {
  0% {
    text-shadow: 0 0 30px rgba(255, 192, 203, 1), 0 0 60px rgba(255, 105, 180, 0.8), 0 4px 8px rgba(0, 0, 0, 0.3);
  }
  100% {
    text-shadow: 0 0 40px rgba(255, 192, 203, 1), 0 0 80px rgba(255, 105, 180, 1), 0 4px 8px rgba(0, 0, 0, 0.3);
  }
}

/* 流星效果 */
.meteors {
  position: absolute;
  width: 100%;
  height: 100%;
  overflow: hidden;
  pointer-events: none;
}

.meteor {
  position: absolute;
  width: 8px;
  height: 8px;
  background: radial-gradient(circle, #fff 0%, rgba(255, 192, 203, 1) 30%, rgba(255, 105, 180, 0.8) 60%, transparent 100%);
  border-radius: 50%;
  box-shadow: 0 0 25px rgba(255, 192, 203, 1), 0 0 50px rgba(255, 105, 180, 0.8);
  filter: drop-shadow(0 0 20px rgba(255, 192, 203, 1));
}

.meteor-1 {
  animation: meteorFall 6s linear infinite;
  top: -50px;
  left: -50px;
}

.meteor-2 {
  animation: meteorFall2 8s linear infinite;
  top: -80px;
  right: -80px;
  animation-delay: 3s;
}

.meteor::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 200px;
  height: 4px;
  background: linear-gradient(90deg, transparent 0%, rgba(255, 192, 203, 1) 10%, rgba(255, 105, 180, 0.9) 30%, rgba(255, 182, 193, 0.7) 60%, transparent 100%);
  transform: translate(-50%, -50%) rotate(45deg);
  border-radius: 50%;
  filter: blur(0.5px);
  box-shadow: 0 0 15px rgba(255, 192, 203, 0.8);
}

@keyframes meteorFall {
  0% {
    transform: translateY(-100px) translateX(-100px) scale(0.5);
    opacity: 0;
  }
  5% {
    opacity: 1;
    transform: translateY(-80px) translateX(-80px) scale(1);
  }
  95% {
    opacity: 1;
    transform: translateY(calc(100vh + 100px)) translateX(calc(100vw + 100px)) scale(1);
  }
  100% {
    transform: translateY(calc(100vh + 150px)) translateX(calc(100vw + 150px)) scale(0.5);
    opacity: 0;
  }
}

@keyframes meteorFall2 {
  0% {
    transform: translateY(-120px) translateX(120px) scale(0.5);
    opacity: 0;
  }
  5% {
    opacity: 1;
    transform: translateY(-100px) translateX(100px) scale(1);
  }
  95% {
    opacity: 1;
    transform: translateY(calc(100vh + 120px)) translateX(calc(-100vw - 120px)) scale(1);
  }
  100% {
    transform: translateY(calc(100vh + 170px)) translateX(calc(-100vw - 170px)) scale(0.5);
    opacity: 0;
  }
}

@keyframes titleGlow {
  0%, 100% {
    text-shadow: 0 0 20px rgba(255, 145, 199, 0.8), 0 0 40px rgba(255, 179, 217, 0.6), 0 0 60px rgba(255, 192, 203, 0.4), 0 0 80px rgba(255, 228, 225, 0.2);
    filter: drop-shadow(0 0 30px rgba(255, 179, 217, 0.5)) blur(0.5px);
  }
  50% {
    text-shadow: 0 0 30px rgba(255, 145, 199, 1), 0 0 60px rgba(255, 179, 217, 0.8), 0 0 90px rgba(255, 192, 203, 0.6), 0 0 120px rgba(255, 228, 225, 0.4);
    filter: drop-shadow(0 0 50px rgba(255, 179, 217, 0.8)) blur(1px);
  }
}

/* 向下箭头提示 */
.scroll-arrow {
  position: fixed;
  bottom: 80px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.arrow-icon {
  width: 0;
  height: 0;
  border-left: 12px solid transparent;
  border-right: 12px solid transparent;
  border-top: 16px solid rgba(255, 192, 203, 0.8);
  animation: arrowBounce 2s ease-in-out infinite;
  filter: drop-shadow(0 0 10px rgba(255, 192, 203, 0.6));
}

.arrow-text {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.8rem;
  text-shadow: 0 0 10px rgba(255, 192, 203, 0.5);
  animation: fadeInOut 3s infinite;
}

@keyframes arrowBounce {
  0%, 100% {
    transform: translateY(0);
    opacity: 0.6;
  }
  50% {
    transform: translateY(10px);
    opacity: 1;
  }
}

@keyframes fadeInOut {
  0%, 100% { opacity: 0.3; }
  50% { opacity: 0.8; }
}

@keyframes gradientFlow {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

@keyframes textBlur {
  0%, 100% {
    filter: drop-shadow(0 0 30px rgba(255, 140, 66, 0.5)) blur(0.5px);
  }
  25% {
    filter: drop-shadow(0 0 35px rgba(255, 140, 66, 0.6)) blur(0.8px);
  }
  50% {
    filter: drop-shadow(0 0 40px rgba(255, 140, 66, 0.7)) blur(1px);
  }
  75% {
    filter: drop-shadow(0 0 35px rgba(255, 140, 66, 0.6)) blur(0.8px);
  }
}

@keyframes subtleBlur {
  0%, 100% {
    filter: blur(0px);
  }
  50% {
    filter: blur(0.3px);
  }
}

@keyframes numberBlur {
  0%, 100% {
    filter: blur(0px);
  }
  50% {
    filter: blur(0.2px);
  }
}

.cursor {
  display: inline-block;
  background: linear-gradient(90deg, #ffc0cb, #ffb6c1);
  margin-left: 3px;
  width: 2px;
  height: 1.2em;
  animation: blink 1s infinite;
  box-shadow: 0 0 10px rgba(255, 192, 203, 0.8);
  filter: drop-shadow(0 0 8px rgba(255, 192, 203, 0.9));
}

@keyframes blink {
  0%, 50% {
    opacity: 1;
  }
  51%, 100% {
    opacity: 0;
  }
}

/* 版权信息 */
.copyright {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(255, 192, 203, 0.1);
  backdrop-filter: blur(10px);
  border-top: 1px solid rgba(255, 192, 203, 0.2);
  padding: 1rem 0;
  text-align: center;
  z-index: 10;
}

.copyright p {
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.9rem;
  margin: 0;
  text-shadow: 0 0 10px rgba(255, 192, 203, 0.5);
  filter: drop-shadow(0 0 5px rgba(255, 192, 203, 0.6));
}

/* 左上角小标题样式 */
.mini-title {
  position: fixed;
  top: 20px;
  left: 20px;
  z-index: 1000;
  background: rgba(255, 20, 147, 0.2);
  backdrop-filter: blur(10px);
  padding: 10px 20px;
  border-radius: 25px;
  border: 1px solid rgba(255, 20, 147, 0.2);
  box-shadow: 0 4px 20px rgba(255, 20, 147, 0.15);
  animation: slideInFromTop 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  cursor: pointer;
  transition: all 0.3s ease;
}

.mini-title:hover {
  background: rgba(255, 20, 147, 0.3);
  transform: translateY(-2px);
  box-shadow: 0 6px 25px rgba(255, 20, 147, 0.25);
}

.mini-title:active {
  transform: translateY(0);
  box-shadow: 0 2px 15px rgba(255, 20, 147, 0.3);
}

.mini-guild-name {
  font-size: 18px;
  font-weight: bold;
  background: linear-gradient(90deg, #ff1493 0%, #ff69b4 25%, #ff91c7 50%, #ff1493 75%, #ff69b4 100%);
  background-size: 300% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  display: inline-block;
  position: relative;
  filter: drop-shadow(0 0 20px rgba(255, 20, 147, 1));
  white-space: nowrap;
  transition: all 0.3s ease;
  animation: gradientMove 3s ease-in-out infinite;
  padding: 0.2em 0.4em;
  border-radius: 15px;
}

/* 页码指示器 */
.page-indicator {
  position: fixed;
  top: 85px;
  left: 30px;
  z-index: 1000;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(15px);
  border-radius: 20px;
  padding: 8px 16px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
  animation: pageIndicatorAppear 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55) 0.2s both;
}

.page-indicator:hover {
  background: rgba(255, 255, 255, 0.25);
  transform: translateY(-2px);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.15);
}

.page-number, .page-separator, .total-pages {
  color: #ffffff;
  font-size: 1rem;
  font-weight: 600;
  text-shadow: 0 0 15px rgba(255, 192, 203, 0.8);
  letter-spacing: 0.3px;
  transition: all 0.3s ease;
  display: inline-block;
}

.page-number {
  animation: pageNumberGlow 2s ease-in-out infinite alternate;
  position: relative;
}

.page-number.changing {
  animation: pageNumberChange 0.3s ease-out, pageNumberGlow 2s ease-in-out infinite alternate;
}

.page-separator {
  margin: 0 4px;
  opacity: 0.7;
}

.total-pages {
  opacity: 0.8;
}

@keyframes pageNumberGlow {
  0% {
    text-shadow: 0 0 15px rgba(255, 192, 203, 0.8), 0 0 25px rgba(255, 105, 180, 0.6);
  }
  100% {
    text-shadow: 0 0 20px rgba(255, 192, 203, 1), 0 0 35px rgba(255, 105, 180, 0.8);
  }
}

@keyframes pageNumberChange {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
    text-shadow: 0 0 25px rgba(255, 192, 203, 1), 0 0 40px rgba(255, 105, 180, 1);
  }
  100% {
    transform: scale(1);
  }
}

@keyframes pageIndicatorAppear {
  0% {
    transform: translateX(-100px) scale(0.8);
    opacity: 0;
    filter: blur(10px);
  }
  50% {
    transform: translateX(10px) scale(1.05);
    opacity: 0.8;
    filter: blur(2px);
  }
  100% {
    transform: translateX(0) scale(1);
    opacity: 1;
    filter: blur(0);
  }
}

@keyframes slideInFromTop {
  0% {
    transform: translateY(-100%) scale(0.8);
    opacity: 0;
  }
  60% {
    transform: translateY(10px) scale(1.05);
    opacity: 0.8;
  }
  100% {
    transform: translateY(0) scale(1);
    opacity: 1;
  }
}

/* 语言通知气泡样式 */
.language-notification {
  position: fixed;
  top: 30px;
  right: 30px;
  z-index: 1001;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(15px);
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 145, 199, 0.3);
  animation: slideInFromRight 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  min-width: 280px;
}

.notification-content {
  text-align: center;
}

.notification-text {
  margin: 0 0 15px 0;
  color: #333;
  font-size: 14px;
  font-weight: 500;
}

.notification-buttons {
  display: flex;
  gap: 10px;
  justify-content: center;
}

.switch-btn, .keep-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.switch-btn {
  background: linear-gradient(45deg, #ff1493, #ff69b4);
  color: white;
}

.switch-btn:hover {
  background: linear-gradient(45deg, #ff69b4, #ff91c7);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(255, 20, 147, 0.4);
}

.keep-btn {
  background: rgba(128, 128, 128, 0.2);
  color: #666;
}

.keep-btn:hover {
  background: rgba(128, 128, 128, 0.3);
  transform: translateY(-2px);
}

@keyframes slideInFromRight {
  0% {
    transform: translateX(100%) scale(0.8);
    opacity: 0;
  }
  60% {
    transform: translateX(-10px) scale(1.05);
    opacity: 0.8;
  }
  100% {
    transform: translateX(0) scale(1);
    opacity: 1;
  }
}

/* 问候气泡样式 */
.greeting-bubble {
  position: fixed;
  top: 50px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1002;
  background: rgba(255, 20, 147, 0.15);
  backdrop-filter: blur(15px);
  -webkit-backdrop-filter: blur(15px);
  border-radius: 20px;
  padding: 12px 24px;
  box-shadow: 0 8px 32px rgba(255, 20, 147, 0.2), 
              inset 0 1px 0 rgba(255, 255, 255, 0.2);
  border: 1px solid rgba(255, 20, 147, 0.25);
  min-width: 280px;
  text-align: center;
}

/* Vue Transition 动画 */
.greeting-enter-active {
  transition: all 0.5s cubic-bezier(0.25, 0.46, 0.45, 0.94);
}

.greeting-leave-active {
  transition: all 0.3s cubic-bezier(0.25, 0.46, 0.45, 0.94);
}

.greeting-enter-from {
  opacity: 0;
  transform: translateX(-50%) translateY(-30px) scale(0.8);
}

.greeting-leave-to {
  opacity: 0;
  transform: translateX(-50%) translateY(-20px) scale(0.9);
}

.greeting-content {
  display: flex;
  align-items: center;
  justify-content: center;
}

.greeting-text {
  margin: 0;
  color: #333;
  font-size: 14px;
  font-weight: 600;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.8);
}





/* 响应式设计 */
@media (max-width: 1200px) {
  .guild-name {
    font-size: 5.5rem;
  }
  
  .slogan-text {
    font-size: 1.8rem;
  }
}

@media (max-width: 768px) {
  .guild-name {
    font-size: 4.5rem;
    text-shadow: 0 0 25px rgba(255, 192, 203, 0.8), 0 0 50px rgba(255, 105, 180, 0.6);
    padding: 0.8rem 1.5rem;
  }
  
  .slogan-text {
    font-size: 1.6rem;
    gap: 0.2rem;
  }
  
  .time-display {
    gap: 1rem;
  }
  
  .time-unit {
    min-width: 80px;
    padding: 1rem;
  }
  
  .time-number {
    font-size: 2rem;
  }
  
  .counter-title {
    font-size: 1.6rem;
  }
}

@media (max-width: 480px) {
  .main-content {
    padding: 1rem;
  }
  
  .guild-name {
    font-size: 3.5rem;
    text-shadow: 0 0 20px rgba(255, 192, 203, 0.8), 0 0 40px rgba(255, 105, 180, 0.6);
    padding: 0.6rem 1rem;
  }
  
  .slogan-text {
    font-size: 1.4rem;
    gap: 0.15rem;
    flex-wrap: wrap;
  }
  
  .time-display {
    gap: 0.8rem;
    justify-content: center;
  }
  
  .time-unit {
    min-width: 70px;
    padding: 0.8rem;
  }
  
  .time-number {
    font-size: 1.6rem;
  }
  
  .time-label {
    font-size: 0.9rem;
  }
  
  .counter-title {
    font-size: 1.4rem;
  }
}

@media (max-width: 320px) {
  .guild-name {
    font-size: 2.8rem;
  }
  
  .slogan-text {
    font-size: 1.2rem;
  }
  
  .time-display {
    gap: 0.5rem;
  }
  
  .time-unit {
    min-width: 60px;
    padding: 0.6rem;
  }
  
  .time-number {
    font-size: 1.4rem;
  }
}
</style>
