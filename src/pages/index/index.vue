<template>
  <view class="container">
    <!-- 血量显示 -->
    <view class="health-container">
      <view class="player-health left" :class="{ 'health-flash': player1Flashing }">
        <text>LP: {{ player1Health }}</text>
      </view>
      <view class="player-health right" :class="{ 'health-flash': player2Flashing }">
        <text>LP: {{ player2Health }}</text>
      </view>
    </view>

    <!-- 滑块切换操作目标 -->
    <view class="slider-container">
      <view class="slider-button" :class="[{ active: selectedPlayer === 1 }, theme]" @click="selectedPlayer = 1">
        玩家 1
      </view>
      <view class="slider-button" :class="[{ active: selectedPlayer === 2 }, theme]" @click="selectedPlayer = 2">
        玩家 2
      </view>
    </view>

    <!-- 按钮网格 -->
    <view class="button-grid">
      <button class="custom-button damage" :class="theme" @click="setAction('damage')">
        受伤
      </button>
      <button class="custom-button heal" :class="theme" @click="setAction('heal')">
        恢复
      </button>
      <button class="custom-button pay" :class="theme" @click="setAction('pay')">
        支付
      </button>
      <button class="custom-button set" :class="theme" @click="setAction('set')">
        设为
      </button>
      <button class="custom-button dice-button" :class="theme" @click="rollDice">
        骰子
      </button>
      <button class="custom-button coin-button" :class="theme" @click="flipCoin">
        硬币
      </button>
    </view>

    <!-- 重置按钮 -->
    <view class="reset-container">
      <button class="custom-button reset" :class="theme" @click="resetHealth">
        重置血量
      </button>
    </view>

    <!-- 数字键盘模态框 -->
    <view class="modal" v-if="showNumberPad" @click="showNumberPad = false">
      <view class="modal-content" :class="theme" @click.stop>
        <view class="input-display">{{ inputNumber }}</view>
        <view class="numpad-grid">
          <button class="numpad-button" :class="theme" v-for="num in numbers" :key="num" @click="selectNumber(num)">
            {{ num }}
          </button>
          <button class="numpad-button" :class="theme" @click="selectNumber('00')">00</button>
          <button class="numpad-button numpad-delete" :class="theme" @click="deleteNumber">
            x
          </button>
          <button class="numpad-button numpad-confirm" :class="theme" @click="confirmNumber">
            确认
          </button>
        </view>
      </view>
    </view>

    <!-- 悬浮球 -->
    <view class="floating-button" :class="theme" @click="showThemeModal = true"
      :style="{ backgroundColor: currentThemeBgColor, color: currentThemeTextColor }">
      🎨
    </view>

    <!-- 主题选择模态框 -->
    <view class="theme-modal" v-if="showThemeModal" @click="closeThemeModalIfOutside($event)">
      <view class="theme-modal-content">
        <view class="theme-modal-header">
          <text>选择主题颜色</text>
        </view>
        <view class="theme-options">
          <view v-for="(themeItem, index) in themes" :key="index" class="theme-option"
            :style="{ backgroundColor: themeItem.bgColor, color: themeItem.textColor }"
            @click="changeTheme(themeItem.name)">
            {{ themeItem.label }}
            <span v-if="theme === themeItem.name" class="checkmark">✔</span>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>
<script>
export default {
  data() {
    return {
      player1Health: 8000,
      player2Health: 8000,
      player1Flashing: false,
      player2Flashing: false,
      selectedPlayer: 1,
      currentAction: null,
      inputNumber: "",
      showNumberPad: false,
      numbers: [1, 2, 3, 4, 5, 6, 7, 8, 9, 0],
      theme: "blue",
      showThemeModal: false,
      themes: [
        {
          name: "blue",
          label: "柔和淡蓝色系",
          bgColor: "#d7e8fc",
          textColor: "#2962ff",
        },
        {
          name: "pink",
          label: "淡粉色系",
          bgColor: "#f8d7e1",
          textColor: "#a63955",
        },
      ],
    };
  },
  computed: {
    currentThemeBgColor() {
      const themeObj = this.themes.find((t) => t.name === this.theme);
      return themeObj ? themeObj.bgColor : "#d7e8fc";
    },
    currentThemeTextColor() {
      const themeObj = this.themes.find((t) => t.name === this.theme);
      return themeObj ? themeObj.textColor : "#2962ff";
    },
  },
  methods: {
    setAction(action) {
      this.currentAction = action;
      this.showNumberPad = true;
      this.inputNumber = "";
    },
    closeModalIfOutside(e) {
      const modalContent = document.querySelector(".modal-content");
      if (modalContent && !modalContent.contains(e.target)) {
        this.showNumberPad = false;
        this.currentAction = null;
        this.inputNumber = "";
      }
    },
    selectNumber(num) {
      this.inputNumber += num.toString();
    },
    deleteNumber() {
      this.inputNumber = this.inputNumber.slice(0, -1);
    },
    confirmNumber() {
      const amount = parseInt(this.inputNumber);
      if (isNaN(amount)) {
        uni.showToast({ title: "请输入有效数字", icon: "none" });
        return;
      }

      if (this.selectedPlayer === 1) {
        this.player1Health = this.calcHealth(this.player1Health, amount, this.currentAction);
        this.triggerFlash(1);
      } else {
        this.player2Health = this.calcHealth(this.player2Health, amount, this.currentAction);
        this.triggerFlash(2);
      }

      this.showNumberPad = false;
      this.currentAction = null;
      this.inputNumber = "";
    },
    calcHealth(currentHealth, amount, action) {
      let health = currentHealth;
      switch (action) {
        case "damage":
        case "pay":
          health = Math.max(0, health - amount);
          break;
        case "heal":
          health += amount;
          break;
        case "set":
          health = amount;
          break;
      }
      return health;
    },
    resetHealth() {
      this.player1Health = 8000;
      this.player2Health = 8000;
      uni.showToast({ title: "血量已重置", icon: "success" });
    },
    rollDice() {
      const result = Math.floor(Math.random() * 6) + 1;
      uni.showToast({ title: `骰子结果: ${result}`, icon: "none" });
    },
    flipCoin() {
      const result = Math.random() < 0.5 ? "正面" : "反面";
      uni.showToast({ title: `硬币结果: ${result}`, icon: "none" });
    },
    changeTheme(themeName) {
      this.theme = themeName;
      this.showThemeModal = false;
    },
    closeThemeModalIfOutside(e) {
      const modalContent = document.querySelector(".theme-modal-content");
      if (modalContent && !modalContent.contains(e.target)) {
        this.showThemeModal = false;
      }
    },
    triggerFlash(player) {
      const key = player === 1 ? "player1Flashing" : "player2Flashing";
      this[key] = true;
      setTimeout(() => {
        this[key] = false;
      }, 3000); // 闪烁3秒
    },
  },
};
</script>


<style scoped>

/* 数字键盘专属样式 */
.numpad-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  width: 100%;
}

.numpad-button {
  padding: 15px 30px;
  font-size: 22px;
  font-weight: 400;
  border-radius: 20px;
  border: none;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* 蓝色主题数字按钮 */
.numpad-button.blue {
  background-color: #e3f2fd;
  color: #1565c0;
}
.numpad-button.blue:active {
  background-color: #bbdefb;
}

/* 粉色主题数字按钮 */
.numpad-button.pink {
  background-color: #fce4ec;
  color: #c2185b;
}
.numpad-button.pink:active {
  background-color: #f8bbd0;
}

/* 删除按钮 */
.numpad-delete {
  background-color: #ffebee !important;
  color: #d32f2f !important;
}
.numpad-delete:active {
  background-color: #ffcdd2 !important;
}

/* 确认按钮 */
.numpad-confirm {
  grid-column: 1 / -1;  /* 这个属性已经正确，让按钮从第一列延伸到最后一列 */
  background-color: #e8f5e9 !important;
  color: #2e7d32 !important;
  font-weight: 700;
  font-size: 20px;
  padding: 15px 0;  /* 确保上下有足够的内边距 */
  width: 100%;  /* 确保宽度是100% */
  margin-top: 5px;  /* 可选：增加与上方按钮的间距 */
}

.numpad-confirm:active {
  background-color: #c8e6c9 !important;
}

/* 调整输入显示区样式 */
.input-display {
  font-size: 40px;
  font-weight: 700;
  margin-bottom: 25px;
  min-height: 60px;
  color: #333;
  user-select: text;
  padding: 10px;
  border-radius: 15px;
  background-color: #f5f5f5;
}

.container {
  padding: 30px 20px 40px 20px;
  background-color: #f9f9f9;
  min-height: 100vh;
  box-sizing: border-box;
}

/* 新增动画 */
@keyframes healthFlash {

  0%,
  100% {
    background-color: transparent;
  }

  50% {
    background-color: rgba(255, 235, 59, 0.5);
  }

  /* 黄色闪光 */
}

.health-flash {
  animation: healthFlash 0.6s ease-in-out;
  animation-iteration-count: 5;
  /* 5 次共 3 秒 */
}

/* 血量区域 */
.health-container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin-bottom: 30px;
  width: 100%;
}

.player-health {
  width: 48%;
  background: #fff;
  padding: 24px 12px;
  border-radius: 12px;
  font-size: 24px;
  font-weight: 700;
  text-align: center;
  user-select: none;
  color: #333;
}

.player-health.left {
  color: #0d47a1;
  /* 鲜明蓝 */
}

.player-health.right {
  color: #b71c1c;
  /* 鲜明红 */
}

/* 滑块切换 */
.slider-container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin-bottom: 30px;
  width: 100%;
}

.slider-button {
  flex: 1;
  margin: 0 10px;
  padding: 16px 0;
  border-radius: 16px;
  font-weight: 600;
  text-align: center;
  cursor: pointer;
  user-select: none;
  transition: background-color 0.3s ease, color 0.3s ease;
  color: #a63955;
  /* 粉色默认 */
  background: #f8d7e1;
}

.slider-button.active {
  background: #f48fb1;
  color: white;
}

/* 蓝色主题覆盖 */
.slider-button.blue {
  background: #d7e8fc;
  color: #2962ff;
}

.slider-button.blue.active {
  background: #64b5f6;
  color: white;
}

/* 按钮网格 */
.button-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-bottom: 20px;
  width: 100%;
}

.custom-button {
  padding: 16px 20px;
  font-weight: 600;
  border-radius: 16px;
  cursor: pointer;
  user-select: none;
  border: none;
  transition: background-color 0.3s ease;
  box-shadow: none;
}

/* 粉色按钮主题 */
.custom-button.pink {
  background-color: #f8d7e1;
  color: #a63955;
}

.custom-button.pink:hover {
  background-color: #f48fb1;
  color: white;
}

/* 蓝色按钮主题 */
.custom-button.blue {
  background-color: #d7e8fc;
  color: #2962ff;
}

.custom-button.blue:hover {
  background-color: #64b5f6;
  color: white;
}

/* 重置按钮 */
.reset-container {
  margin-bottom: 30px;
}

.custom-button.reset.pink {
  width: 100%;
  background-color: #f8d7e1;
  color: #a63955;
}

.custom-button.reset.blue {
  width: 100%;
  background-color: #d7e8fc;
  color: #2962ff;
}

/* 模态框背景半透明 */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.35);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

/* 模态框内容，背景白色固定 */
.modal-content {
  background-color: white !important;
  padding: 20px 30px;
  border-radius: 12px;
  box-sizing: border-box;
  min-width: 320px;
  max-width: 90vw;
  text-align: center;
  color: #333;
  user-select: none;
}

/* 数字输入显示 */
.input-display {
  font-size: 32px;
  font-weight: 700;
  margin-bottom: 20px;
  min-height: 48px;
  color: #333;
  user-select: text;
}

/* 数字键盘网格 */
.number-pad {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}

/* 数字键按钮 */
.number-pad .custom-button {
  padding: 14px 0;
  font-size: 20px;
  border-radius: 12px;
  user-select: none;
}

/* 删除按钮 */
.delete-button {
  background-color: #f44336 !important;
  color: white !important;
}

.delete-button:hover {
  background-color: #d32f2f !important;
}

.confirm-button {
  grid-column: 1 / -1;
  background-color: rgb(0, 200, 83) !important;
  /* 更鲜艳的亮绿色 */
  color: white !important;
  font-weight: 700;
  transition: background-color 0.3s ease;
}

.confirm-button:hover {
  background-color: rgb(0, 170, 70) !important;
  /* 悬浮时稍暗一点 */
}

/* 悬浮球 */
.floating-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 54px;
  height: 54px;
  border-radius: 50%;
  font-size: 28px;
  line-height: 54px;
  text-align: center;
  cursor: pointer;
  user-select: none;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
  transition: background-color 0.3s ease, color 0.3s ease;
}

/* 主题弹窗 */
.theme-modal {
  position: fixed;
  inset: 0;
  background-color: rgba(0, 0, 0, 0.35);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1100;
}

.theme-modal-content {
  background: white;
  border-radius: 12px;
  padding: 20px 30px;
  width: 300px;
  box-sizing: border-box;
  user-select: none;
}

.theme-modal-header {
  font-weight: 700;
  font-size: 22px;
  margin-bottom: 16px;
  text-align: center;
  color: #333;
}

.theme-options {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.theme-option {
  padding: 12px 20px;
  border-radius: 12px;
  cursor: pointer;
  font-weight: 600;
  position: relative;
  user-select: none;
}

.theme-option:hover {
  opacity: 0.8;
}

.checkmark {
  position: absolute;
  right: 16px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 18px;
  color: inherit;
  user-select: none;
}

uni-text {
  display: inline;
  text-align: center;
}
</style>
