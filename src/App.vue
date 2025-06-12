<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const showMessage = ref(false)
const showReasons = ref(false)
const showFinalMessage = ref(false)
const currentHeartIndex = ref(0)
const photosVisible = ref(false)
const scrollY = ref(0)
const showButtons = ref(false)
const userAnswer = ref('')
const showMobileWarning = ref(false)
const buttonClicked = ref(false)

const reasons = [
  'Мой дом в любой точке мира',
  'Причина моей искренней улыбки',
  'Самая красивая глава в книге моей жизни',
  'Ты — мой человек. Как бы банально ни звучало'
]

onMounted(() => {
  // Проверяем мобильное устройство
  checkMobileDevice()
  
  // Создаем падающие сердечки
  createFallingHearts()
  
  // Показываем фотографии с задержкой
  setTimeout(() => {
    photosVisible.value = true
  }, 200)
  
  // Отслеживаем скролл
  window.addEventListener('scroll', handleScroll)
})

const checkMobileDevice = () => {
  const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent) || window.innerWidth <= 768
  
  if (isMobile) {
    showMobileWarning.value = true
    // Скрываем через 2 секунды с анимацией исчезновения
    setTimeout(() => {
      const warningElement = document.querySelector('.mobile-warning')
      if (warningElement) {
        warningElement.style.animation = 'slideUpWarning 0.5s ease-in forwards'
      }
      setTimeout(() => {
        showMobileWarning.value = false
      }, 500)
    }, 2000)
  }
}

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

const handleScroll = () => {
  scrollY.value = window.scrollY
  
  // Плавно растворяем фотографии только когда доскроллили до секции "Я тебя люблю"
  const loveSection = document.querySelector('.love-message')
  
  if (loveSection && showMessage.value) {
    const loveSectionTop = loveSection.offsetTop
    const windowHeight = window.innerHeight
    
    // Скрываем фотографии когда секция "Я тебя люблю" появляется в viewport
    if (scrollY.value + windowHeight / 2 > loveSectionTop) {
      photosVisible.value = false
    } else {
      photosVisible.value = true
    }
  } else {
    // Если секция еще не появилась, фотографии остаются видимыми
    photosVisible.value = true
  }
}

const revealMessage = () => {
  buttonClicked.value = true
  setTimeout(() => {
    showMessage.value = true
    setTimeout(() => {
      showReasons.value = true
    }, 2000)
  }, 1000)
}

const showFinal = () => {
  showFinalMessage.value = true
  setTimeout(() => {
    showButtons.value = true
  }, 2000)
}

const handleYes = () => {
  // Открываем Telegram с готовым сообщением
  const message = "я согласна"
  const telegramUsername = "Kggghoul" // замени на свой username в Telegram
  const telegramUrl = `https://t.me/${telegramUsername}?text=${encodeURIComponent(message)}`
  
  window.open(telegramUrl, '_blank')
}

const handleNo = () => {
  showButtons.value = false
  userAnswer.value = 'no'
}

const createFallingHearts = () => {
  setInterval(() => {
    const heart = document.createElement('div')
    heart.innerHTML = '💖'
    heart.classList.add('falling-heart')
    heart.style.left = Math.random() * 100 + '%'
    heart.style.animationDuration = (Math.random() * 3 + 2) + 's'
    document.body.appendChild(heart)
    
    setTimeout(() => {
      if (heart.parentNode) {
        heart.parentNode.removeChild(heart)
      }
    }, 5000)
  }, 800)
}
</script>

<template>
  <div class="romantic-site">
    <!-- Мобильное предупреждение -->
    <div class="mobile-warning" v-if="showMobileWarning">
      <p class="warning-text">посмотри потом с компа, там лучше выглядит))</p>
    </div>
    
    <!-- Фотографии Лизы - анимированные -->
    <div class="photo-frame left-photo" :class="{ 'visible': photosVisible, 'hidden': !photosVisible }">
      <div class="photo-polaroid">
        <img src="/liza-photo-1.jpg" alt="Моя любимая Лиза" class="photo-img" />
        <div class="photo-caption">Ты прекрасна</div>
      </div>
    </div>
    
    <div class="photo-frame right-photo" :class="{ 'visible': photosVisible, 'hidden': !photosVisible }">
      <div class="photo-polaroid">
        <img src="/liza-photo-2.jpg" alt="Любимая Лиза" class="photo-img" />
        <div class="photo-caption">Ты прекрасна</div>
      </div>
    </div>

    <!-- Главный экран -->
    <section class="hero-section">
      <div class="hero-content">
        <h1 class="main-title">
          <span class="highlight">Для моей любимой</span>
          <span class="name">Лизы</span>
          <span class="heart">💕</span>
        </h1>
        <p class="subtitle">хочу раскрыть тебе секрет</p>
        <button @click="revealMessage" class="reveal-btn" v-if="!showMessage && !buttonClicked">
          Нажимай ))
        </button>
        <p class="scroll-hint" v-if="buttonClicked">
          листай вниз красотка 💕
        </p>
      </div>
    </section>

    <!-- Сообщение о любви -->
    <section class="love-message" v-if="showMessage" :class="{ 'visible': showMessage }">
      <div class="message-content">
        <h2 class="love-title">Я люблю тебя</h2>
        <p class="love-text">
          Лиза, ты появилась в моей жизни совсем недавно, я бы хотел сказать что не хочу торопить события, но сильнее не хочу соврать. Ты мне действительно очень нравишься
        </p>
      </div>
    </section>

    <!-- Причины любви -->
    <section class="reasons-section" v-if="showReasons" :class="{ 'visible': showReasons }">
      <h3 class="reasons-title">Что ты значишь для меня...</h3>
      <div class="reasons-list">
        <div 
          v-for="(reason, index) in reasons" 
          :key="index"
          class="reason-item"
          :style="{ animationDelay: `${index * 0.3}s` }"
        >
          {{ reason }}
        </div>
      </div>
              <button @click="showFinal" class="final-btn" v-if="!showFinalMessage">
          И самое главное...
        </button>
    </section>

    <!-- Финальное сообщение -->
    <section class="final-section" v-if="showFinalMessage" :class="{ 'visible': showFinalMessage }">
      <div class="final-content">
        <h2 class="final-title">???</h2>
        <p class="final-text">
          Жанм, ты прекрасна, и я хочу чтобы ты была моей
        </p>
        <div class="hearts-container">
          <span class="big-heart">❤️</span>
          <span class="big-heart">💖</span>
          <span class="big-heart">💕</span>
        </div>
        <p class="signature">Ты согласна быть моей?</p>
        
        <!-- Кнопки ответа -->
        <div class="answer-buttons" v-if="showButtons && userAnswer !== 'no'">
          <button @click="handleYes" class="answer-btn yes-btn">
            Да
          </button>
          <button @click="handleNo" class="answer-btn no-btn">
            Нет
          </button>
        </div>
        
        <!-- Грустное сообщение -->
        <div class="sad-message" v-if="userAnswer === 'no'">
          <p class="sad-text">ну лан((</p>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
/* Базовые стили */
.romantic-site {
  min-height: 100vh;
  font-family: 'Georgia', 'Times New Roman', serif;
  overflow-x: hidden;
  position: relative;
}

/* Мобильное предупреждение */
.mobile-warning {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(10px);
  z-index: 9999;
  padding: 1rem;
  text-align: center;
  animation: slideDownWarning 0.5s ease-out;
}

.warning-text {
  color: #fff;
  font-family: 'Georgia', serif;
  font-size: 1rem;
  margin: 0;
  font-style: italic;
  text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
}

@keyframes slideDownWarning {
  from {
    transform: translateY(-100%);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes slideUpWarning {
  from {
    transform: translateY(0);
    opacity: 1;
  }
  to {
    transform: translateY(-100%);
    opacity: 0;
  }
}

.hero-section {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  text-align: center;
}

.hero-content {
  position: relative;
}

.main-title {
  font-weight: bold;
  margin-bottom: 2rem;
  line-height: 1.2;
  font-family: 'Georgia', serif;
}

.highlight {
  display: block;
  color: #fff;
  animation: glow 2s ease-in-out infinite alternate;
}

.name {
  display: block;
  color: #e91e63;
  animation: bounce 1.5s ease-in-out infinite alternate;
  font-family: 'Georgia', serif;
  font-style: italic;
}

.heart {
  display: block;
  animation: pulse 1.5s ease-in-out infinite;
}

.subtitle {
  color: #fff;
  font-style: italic;
  font-family: 'Georgia', serif;
}

.scroll-hint {
  color: #fff;
  font-style: italic;
  font-family: 'Georgia', serif;
  opacity: 0;
  animation: fadeInHint 0.8s ease-in-out forwards;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.4);
  margin-bottom: 3rem;
}

@keyframes fadeInHint {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.reveal-btn, .final-btn {
  background: linear-gradient(45deg, #e91e63, #f06292, #ff69b4);
  color: white;
  border: none;
  border-radius: 30px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-family: 'Georgia', serif;
  position: relative;
  overflow: hidden;
}

.love-message, .reasons-section, .final-section {
  text-align: center;
  margin: 0 auto;
  opacity: 0;
  transform: translateY(50px);
  transition: all 1s ease;
}

.love-message.visible, .reasons-section.visible, .final-section.visible {
  opacity: 1;
  transform: translateY(0);
}

.love-title, .final-title, .reasons-title {
  color: #e91e63;
  font-family: 'Georgia', serif;
  position: relative;
}

.love-text, .final-text {
  color: #fff;
  line-height: 1.6;
  font-family: 'Georgia', serif;
}

.reasons-list {
  display: grid;
  gap: 1.5rem;
}

.reason-item {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  color: #e91e63;
  box-shadow: 0 6px 20px rgba(0,0,0,0.15);
  animation: slideInUp 0.8s ease forwards;
  opacity: 0;
  transform: translateY(30px);
  font-family: 'Georgia', serif;
  border: 2px solid rgba(233, 30, 99, 0.2);
  position: relative;
}

.hearts-container {
  margin: 2rem 0;
}

.big-heart {
  animation: pulse 1.5s ease-in-out infinite;
  display: inline-block;
}

.big-heart:nth-child(1) { animation-delay: 0s; }
.big-heart:nth-child(2) { animation-delay: 0.2s; }
.big-heart:nth-child(3) { animation-delay: 0.4s; }

.signature {
  color: #fff;
  font-style: italic;
  font-family: 'Georgia', serif;
  position: relative;
}

/* Стили для кнопок ответа */
.answer-buttons {
  display: flex;
  justify-content: center;
  gap: 2rem;
  margin-top: 3rem;
  opacity: 0;
  animation: fadeInButtons 1s ease-in-out 0.5s forwards;
}

.answer-btn {
  padding: 1rem 2rem;
  font-size: 1.2rem;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  font-family: 'Georgia', serif;
  font-weight: bold;
  transition: all 0.3s ease;
  min-width: 120px;
}

.yes-btn {
  background: linear-gradient(45deg, #4CAF50, #66BB6A);
  color: white;
  box-shadow: 0 4px 15px rgba(76, 175, 80, 0.4);
}

.yes-btn:hover {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 8px 25px rgba(76, 175, 80, 0.6);
  background: linear-gradient(45deg, #66BB6A, #4CAF50);
}

.no-btn {
  background: linear-gradient(45deg, #f44336, #e57373);
  color: white;
  box-shadow: 0 4px 15px rgba(244, 67, 54, 0.4);
}

.no-btn:hover {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 8px 25px rgba(244, 67, 54, 0.6);
  background: linear-gradient(45deg, #e57373, #f44336);
}

.sad-message {
  margin-top: 3rem;
  opacity: 0;
  animation: fadeInSad 1s ease-in-out forwards;
}

.sad-text {
  font-size: 2rem;
  color: #fff;
  font-family: 'Georgia', serif;
  font-style: italic;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.4);
}

@keyframes fadeInButtons {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInSad {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

/* Стили для фотографий */
.photo-frame {
  position: fixed;
  z-index: 100;
  transition: all 1.2s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  opacity: 0;
}

.left-photo {
  left: 2rem;
  top: 20%;
  --rotation: -8deg;
  transform: translateX(-250px) rotate(-180deg) scale(0.5);
  animation-delay: 0s;
}

.left-photo.visible {
  transform: translateX(0) rotate(-8deg) scale(1);
  opacity: 1;
  animation: photoFloatLeft 4s ease-in-out infinite 1.5s;
}

.left-photo.hidden {
  transform: translateX(0) rotate(-8deg) scale(1);
  opacity: 0;
  transition: opacity 1.5s ease-out;
}

.right-photo {
  right: 2rem;
  top: 60%;
  --rotation: 12deg;
  transform: translateX(250px) rotate(180deg) scale(0.5);
  animation-delay: 0.3s;
}

.right-photo.visible {
  transform: translateX(0) rotate(12deg) scale(1);
  opacity: 1;
  animation: photoFloatRight 4s ease-in-out infinite 1.8s;
}

.right-photo.hidden {
  transform: translateX(0) rotate(12deg) scale(1);
  opacity: 0;
  transition: opacity 1.5s ease-out;
}

.photo-polaroid {
  background: #fff;
  padding: 1rem 1rem 3rem 1rem;
  box-shadow: 
    0 4px 8px rgba(0,0,0,0.2),
    0 8px 16px rgba(0,0,0,0.1),
    inset 0 1px 0 rgba(255,255,255,0.5);
  border-radius: 2px;
  position: relative;
  max-width: 280px;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  cursor: pointer;
  overflow: hidden;
}

.photo-polaroid::after {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent);
  transition: left 0.6s;
}

.photo-polaroid:hover {
  transform: scale(1.1) rotate(0deg) translateY(-10px);
  z-index: 1000;
  box-shadow: 
    0 15px 30px rgba(233, 30, 99, 0.3),
    0 10px 20px rgba(0,0,0,0.2);
}

.photo-polaroid:hover::after {
  left: 100%;
}

.photo-frame.visible .photo-polaroid {
  animation: photoShine 3s ease-in-out infinite 2s;
}

@keyframes photoShine {
  0%, 90%, 100% { 
    box-shadow: 
      0 4px 8px rgba(0,0,0,0.2),
      0 8px 16px rgba(0,0,0,0.1);
  }
  95% { 
    box-shadow: 
      0 4px 8px rgba(0,0,0,0.2),
      0 8px 16px rgba(0,0,0,0.1),
      0 0 20px rgba(255,182,193,0.6);
  }
}

.photo-polaroid::before {
  content: '';
  position: absolute;
  top: 0.5rem;
  left: 50%;
  transform: translateX(-50%);
  width: 2rem;
  height: 0.3rem;
  background: #ddd;
  border-radius: 0.15rem;
}

.photo-img {
  width: 100%;
  height: 220px;
  object-fit: cover;
  border-radius: 1px;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-family: 'Georgia', serif;
  font-size: 0.8rem;
}

.photo-img::before {
  content: '📷 Фото Лизы';
}

.photo-caption {
  text-align: center;
  margin-top: 0.5rem;
  font-family: 'Georgia', serif;
  font-size: 0.9rem;
  color: #666;
  font-style: italic;
}

/* Десктопные стили */
@media (min-width: 1024px) {
  .romantic-site {
    background: linear-gradient(135deg, #ffb6c1 0%, #ffc0cb 25%, #ffb6db 50%, #ffc8dd 75%, #ffd1dc 100%);
  }
  
  .hero-section {
    padding: 4rem;
    background: radial-gradient(circle at 50% 50%, rgba(255,255,255,0.1) 0%, transparent 70%);
  }
  
  .hero-content {
    max-width: 900px;
  }
  
  .hero-content::before {
    content: '🌹';
    position: absolute;
    top: -50px;
    left: -100px;
    font-size: 4rem;
    opacity: 0.3;
    animation: rotate 6s linear infinite;
  }
  
  .hero-content::after {
    content: '🌹';
    position: absolute;
    bottom: -50px;
    right: -100px;
    font-size: 4rem;
    opacity: 0.3;
    animation: rotate 6s linear infinite reverse;
  }
  
  .main-title {
    font-size: 5rem;
    margin-bottom: 3rem;
  }
  
  .highlight {
    text-shadow: 3px 3px 8px rgba(0,0,0,0.4);
    animation: glow 3s ease-in-out infinite alternate;
    font-size: 4rem;
    margin-bottom: 1rem;
  }
  
  .name {
    font-size: 6rem;
    text-shadow: 4px 4px 10px rgba(0,0,0,0.4);
    animation: bounce 2s ease-in-out infinite alternate;
    margin: 2rem 0;
  }
  
  .heart {
    font-size: 4rem;
    animation: pulse 2s ease-in-out infinite;
    margin-top: 1rem;
  }
  
  .subtitle {
    font-size: 2rem;
    margin-bottom: 4rem;
    text-shadow: 2px 2px 4px rgba(0,0,0,0.4);
  }
  
  .scroll-hint {
    font-size: 1.8rem;
    margin-bottom: 4rem;
  }
  
  .reveal-btn, .final-btn {
    padding: 1.5rem 3rem;
    font-size: 1.4rem;
    border-radius: 50px;
    box-shadow: 0 6px 25px rgba(233, 30, 99, 0.4);
  }
  
  .reveal-btn::before, .final-btn::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
    transition: left 0.5s;
  }
  
  .reveal-btn:hover::before, .final-btn:hover::before {
    left: 100%;
  }
  
  .reveal-btn:hover, .final-btn:hover {
    transform: translateY(-5px) scale(1.05);
    box-shadow: 0 10px 30px rgba(233, 30, 99, 0.6);
  }
  
  .love-message, .reasons-section, .final-section {
    padding: 6rem 4rem;
    max-width: 1000px;
    transform: translateY(80px);
    transition: all 1.5s ease;
  }
  
  .love-title, .final-title {
    font-size: 4rem;
    margin-bottom: 3rem;
    text-shadow: 3px 3px 8px rgba(0,0,0,0.3);
  }
  
  .love-title::after, .final-title::after {
    content: '💕';
    position: absolute;
    top: -20px;
    right: -80px;
    font-size: 2rem;
    animation: float 3s ease-in-out infinite;
  }
  
  .love-text, .final-text {
    font-size: 1.6rem;
    line-height: 1.8;
    text-shadow: 2px 2px 4px rgba(0,0,0,0.4);
    margin-bottom: 3rem;
    max-width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
  
  .reasons-title {
    font-size: 3rem;
    margin-bottom: 4rem;
    text-shadow: 3px 3px 8px rgba(0,0,0,0.3);
  }
  
  .reasons-title::before {
    content: '✨';
    position: absolute;
    left: -60px;
    top: 50%;
    transform: translateY(-50%);
    animation: sparkle 2s ease-in-out infinite;
  }
  
  .reasons-title::after {
    content: '✨';
    position: absolute;
    right: -60px;
    top: 50%;
    transform: translateY(-50%);
    animation: sparkle 2s ease-in-out infinite 1s;
  }
  
  .reasons-list {
    grid-template-columns: repeat(2, 1fr);
    gap: 2rem;
    margin-bottom: 4rem;
  }
  
  .reason-item {
    padding: 2rem;
    border-radius: 25px;
    font-size: 1.3rem;
    box-shadow: 0 8px 25px rgba(0,0,0,0.15);
    animation: slideInUp 1s ease forwards;
    transform: translateY(50px);
    overflow: hidden;
  }
  
  .reason-item::before {
    content: '';
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background: radial-gradient(circle, rgba(255,182,193,0.1) 0%, transparent 70%);
    animation: shimmer 4s ease-in-out infinite;
  }
  
  .hearts-container {
    margin: 3rem 0;
  }
  
  .big-heart {
    font-size: 4rem;
    margin: 0 2rem;
    animation: pulse 2s ease-in-out infinite;
  }
  
  .big-heart:nth-child(1) { animation-delay: 0s; }
  .big-heart:nth-child(2) { animation-delay: 0.3s; }
  .big-heart:nth-child(3) { animation-delay: 0.6s; }
  
  .signature {
    font-size: 1.5rem;
    text-shadow: 2px 2px 4px rgba(0,0,0,0.4);
  }
  
  .signature::before {
    content: '🌟';
    position: absolute;
    left: -40px;
    animation: twinkle 2s ease-in-out infinite;
  }
  
  .signature::after {
    content: '🌟';
    position: absolute;
    right: -40px;
    animation: twinkle 2s ease-in-out infinite 1s;
  }
}

/* Мобильные стили */
@media (max-width: 1023px) {
  .romantic-site {
    background: linear-gradient(180deg, #ff8a95 0%, #ffb3ba 30%, #ffcccb 70%, #ffd1dc 100%);
  }
  
  .hero-section {
    padding: 2rem 1rem;
    background: radial-gradient(ellipse at center, rgba(255,255,255,0.1) 0%, transparent 60%);
  }
  
  .hero-content {
    max-width: 100%;
  }
  
  .hero-content::before {
    content: '🌸';
    position: absolute;
    top: -30px;
    left: 10px;
    font-size: 2rem;
    opacity: 0.4;
    animation: float 4s ease-in-out infinite;
  }
  
  .hero-content::after {
    content: '🌸';
    position: absolute;
    bottom: -30px;
    right: 10px;
    font-size: 2rem;
    opacity: 0.4;
    animation: float 4s ease-in-out infinite 2s;
  }
  
  .main-title {
    font-size: 2.5rem;
    line-height: 1.3;
  }
  
  .highlight {
    text-shadow: 2px 2px 6px rgba(0,0,0,0.5);
    font-size: 2rem;
    margin-bottom: 0.5rem;
  }
  
  .name {
    font-size: 3.5rem;
    text-shadow: 3px 3px 8px rgba(0,0,0,0.5);
    margin: 1rem 0;
  }
  
  .heart {
    font-size: 2.5rem;
    margin-top: 0.5rem;
  }
  
  .subtitle {
    font-size: 1.3rem;
    margin-bottom: 2.5rem;
    text-shadow: 1px 1px 3px rgba(0,0,0,0.5);
    padding: 0 1rem;
  }
  
  .scroll-hint {
    font-size: 1.2rem;
    margin-bottom: 2.5rem;
    padding: 0 1rem;
  }
  
  .reveal-btn, .final-btn {
    padding: 1.2rem 2.5rem;
    font-size: 1.1rem;
    box-shadow: 0 4px 20px rgba(233, 30, 99, 0.4);
    margin: 1rem;
  }
  
  .reveal-btn:active, .final-btn:active {
    transform: scale(0.98);
    box-shadow: 0 2px 10px rgba(233, 30, 99, 0.6);
  }
  
  .love-message, .reasons-section, .final-section {
    padding: 3rem 1.5rem;
    max-width: 100%;
  }
  
  .love-title, .final-title {
    font-size: 2.5rem;
    margin-bottom: 2rem;
    text-shadow: 2px 2px 6px rgba(0,0,0,0.3);
  }
  
  .love-title::after, .final-title::after {
    content: '💕';
    position: absolute;
    top: -15px;
    right: -30px;
    font-size: 1.5rem;
    animation: float 2s ease-in-out infinite;
  }
  
  .love-text, .final-text {
    font-size: 1.2rem;
    text-shadow: 1px 1px 3px rgba(0,0,0,0.5);
    margin-bottom: 2rem;
    padding: 0 0.5rem;
  }
  
  .reasons-title {
    font-size: 2rem;
    margin-bottom: 2.5rem;
    text-shadow: 2px 2px 6px rgba(0,0,0,0.3);
  }
  
  .reasons-title::before, .reasons-title::after {
    content: '✨';
    position: absolute;
    top: -10px;
    animation: sparkle 2s ease-in-out infinite;
    font-size: 1rem;
  }
  
  .reasons-title::before {
    left: -25px;
  }
  
  .reasons-title::after {
    right: -25px;
    animation-delay: 1s;
  }
  
  .reasons-list {
    margin-bottom: 3rem;
  }
  
  .reason-item {
    padding: 1.5rem;
    font-size: 1.1rem;
  }
  
  .reason-item::before {
    content: '';
    position: absolute;
    top: -2px;
    left: -2px;
    right: -2px;
    bottom: -2px;
    background: linear-gradient(45deg, #ff8a95, #ffb3ba, #ff8a95);
    border-radius: 20px;
    z-index: -1;
    opacity: 0;
    transition: opacity 0.3s ease;
  }
  
  .reason-item:hover::before {
    opacity: 0.3;
  }
  
  .big-heart {
    font-size: 2.5rem;
    margin: 0 0.5rem;
  }
  
  .signature {
    font-size: 1.1rem;
    text-shadow: 1px 1px 3px rgba(0,0,0,0.5);
    padding: 0 1rem;
  }
  
  .signature::before, .signature::after {
    content: '🌟';
    position: absolute;
    animation: twinkle 2s ease-in-out infinite;
    font-size: 0.8rem;
  }
  
  .signature::before {
    left: 0;
    top: -5px;
  }
  
  .signature::after {
    right: 0;
    top: -5px;
    animation-delay: 1s;
  }
  
  /* Мобильные стили для кнопок */
  .answer-buttons {
    flex-direction: column;
    gap: 1rem;
    margin-top: 2rem;
  }
  
  .answer-btn {
    padding: 0.8rem 1.5rem;
    font-size: 1rem;
    min-width: 100px;
  }
  
  .sad-text {
    font-size: 1.5rem;
  }
  
  /* Адаптация фотографий для мобильных */
  .left-photo {
    left: 0.5rem;
    top: 10%;
    transform: translateX(-200px) rotate(-120deg) scale(0.3);
  }
  
  .left-photo.visible {
    transform: translateX(0) rotate(-8deg) scale(0.75);
    animation: photoFloatLeftMobile 3s ease-in-out infinite 1.5s;
  }
  
  .left-photo.hidden {
    transform: translateX(0) rotate(-8deg) scale(0.75);
    opacity: 0;
    transition: opacity 1.2s ease-out;
  }
  
  .right-photo {
    right: 0.5rem;
    top: 10%;
    transform: translateX(200px) rotate(120deg) scale(0.3);
  }
  
  .right-photo.visible {
    transform: translateX(0) rotate(10deg) scale(0.75);
    animation: photoFloatRightMobile 3s ease-in-out infinite 1.8s;
  }
  
  .right-photo.hidden {
    transform: translateX(0) rotate(10deg) scale(0.75);
    opacity: 0;
    transition: opacity 1.2s ease-out;
  }
  
  .photo-polaroid {
    max-width: 140px;
  }
  
  .photo-img {
    height: 120px;
    font-size: 0.7rem;
  }
  
  .photo-caption {
    font-size: 0.8rem;
    margin-top: 0.3rem;
  }
}

/* Анимации */
@keyframes glow {
  from { 
    text-shadow: 2px 2px 4px rgba(0,0,0,0.3); 
  }
  to { 
    text-shadow: 2px 2px 20px rgba(255,255,255,0.6), 0 0 30px rgba(255,182,193,0.8); 
  }
}

@keyframes bounce {
  from { transform: translateY(0); }
  to { transform: translateY(-15px); }
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.15); }
}

@keyframes slideInUp {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes float {
  0%, 100% { transform: translateY(0px); }
  50% { transform: translateY(-10px); }
}

@keyframes sparkle {
  0%, 100% { opacity: 0.3; transform: scale(1); }
  50% { opacity: 1; transform: scale(1.2); }
}

@keyframes twinkle {
  0%, 100% { opacity: 0.5; }
  50% { opacity: 1; transform: scale(1.1); }
}

@keyframes rotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

@keyframes shimmer {
  0% { transform: translateX(-100%) translateY(-100%) rotate(45deg); }
  100% { transform: translateX(100%) translateY(100%) rotate(45deg); }
}

@keyframes photoFloatLeft {
  0%, 100% { 
    transform: translateX(0) rotate(-8deg) scale(1) translateY(0px);
  }
  25% {
    transform: translateX(5px) rotate(-6deg) scale(1.02) translateY(-5px);
  }
  50% { 
    transform: translateX(0) rotate(-10deg) scale(1.05) translateY(-8px);
  }
  75% {
    transform: translateX(-3px) rotate(-5deg) scale(1.02) translateY(-3px);
  }
}

@keyframes photoFloatRight {
  0%, 100% { 
    transform: translateX(0) rotate(12deg) scale(1) translateY(0px);
  }
  25% {
    transform: translateX(-5px) rotate(15deg) scale(1.02) translateY(-3px);
  }
  50% { 
    transform: translateX(0) rotate(10deg) scale(1.05) translateY(-10px);
  }
  75% {
    transform: translateX(3px) rotate(14deg) scale(1.02) translateY(-5px);
  }
}

@keyframes photoFloatLeftMobile {
  0%, 100% { 
    transform: translateX(0) rotate(-8deg) scale(0.75) translateY(0px);
  }
  50% { 
    transform: translateX(0) rotate(-10deg) scale(0.77) translateY(-5px);
  }
}

@keyframes photoFloatRightMobile {
  0%, 100% { 
    transform: translateX(0) rotate(10deg) scale(0.75) translateY(0px);
  }
  50% { 
    transform: translateX(0) rotate(12deg) scale(0.77) translateY(-5px);
  }
}
</style>

<style>
body {
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

.falling-heart {
  position: fixed;
  top: -50px;
  font-size: 1.5rem;
  animation: fall linear;
  pointer-events: none;
  z-index: 1000;
}

@keyframes fall {
  0% {
    transform: translateY(-50px) rotate(0deg);
    opacity: 1;
  }
  100% {
    transform: translateY(100vh) rotate(360deg);
    opacity: 0;
  }
}
</style>
