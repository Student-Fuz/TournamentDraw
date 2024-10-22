<template>
  <div class="general_container" :style="{ backgroundImage: `url(${backgroundImageUrl})` }">
    <div class="absolute_temp_container">
      <!-- 抽签结果展示 -->
      <h1>中午吃什么抽签</h1>

      <!-- 显示 DishCard，无论是否已抽签 -->
      <div class="selected-Dish">
        <DishCard
          :name="selectedDish?.name || ''"
          :picUrl="selectedDish?.picUrl || blankDishUrl"
          :slogan="selectedDish?.slogan || ''"
        />
      </div>

      <!-- 一键抽签按钮 -->
      <button @click="startDraw">一键抽签</button>

      <h2>成电菜单大全</h2>
      
      <AddDishModal
        :show="showModal"
        @close="showModal = false"
        @add-dish="handleAddingDish"
      />

      <div class="gray-note">
        注：双击删除选项
      </div>

      <!-- 展示所有选手的 DishCard -->
      <div class="dishes-grid">
        <DishCard
          v-for="Dish in dishes"
          :key="Dish.id"
          :name="Dish.name"
          :picUrl="Dish.picUrl"
          :slogan="Dish.slogan"
          @remove="deleteDish(Dish.id)"
        />
        <AddingDishCard
          :picUrl="blankDishUrl"
          @onAdd="showModal = true"
        />
      </div>

      <div class="save-button" @click="saveDishesToLocalStorage">保存菜单</div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, onMounted } from 'vue';
import type { Dish } from '../types/Dish';
import DishCard from '../components/DishCard.vue';
import AddingDishCard from '../components/AddingDishCard.vue';
import AddDishModal from '../components/AddDishModal.vue';

export default defineComponent({
  name: 'LaunchDraw',
  components: {
    DishCard,
    AddingDishCard,
    AddDishModal
  },
  setup() {
    const backgroundImageUrl = ref<string>('background_0.png');
    const dishesListUrl = ref<string>('menu.json');
    const blankDishUrl = ref<string>('avatars/blank.png');
    const dishes = ref<Dish[]>([]);
    const selectedDish = ref<Dish | null>(null);
    const showModal = ref(false);
    const drawing = ref(false);
    let timerId: number | null = null;
    let delay = 10; // 初始切换速度

    // 读取选手信息
    const fetchDishes = async () => {
      try {
        const savedDishes = localStorage.getItem('dishes');
        if (savedDishes) {
          dishes.value = JSON.parse(savedDishes);
        }
        else{
          const response = await fetch(dishesListUrl.value);
          if (!response.ok) {
            throw new Error('Failed to fetch dishes data');
          }
          dishes.value = await response.json();
        }
      } catch (error) {
        console.error('Error loading dishes:', error);
      }
    };

    // 初始化
    onMounted(() => {
      fetchDishes();
      // loadDishesFromLocalStorage();
    });

    // 添加选手
    const handleAddingDish = (newDish: Dish) => {
      dishes.value.push(newDish);
      showModal.value = false;
    };

    // 删除菜
    const deleteDish = (dishId: number) => {
      console.log("hello")
      dishes.value = dishes.value.filter(Dish => Dish.id !== dishId);
    };

    // 开始抽签过程
    const startDraw = () => {
      if (dishes.value.length === 0 || drawing.value) return; // 防止重复抽签
      drawing.value = true;
      delay = 10; // 重置速度
      rolldishes();
    };

    // 递减速度的滚动抽签
    const rolldishes = () => {
      timerId = setTimeout(() => {
        const randomIndex = Math.floor(Math.random() * dishes.value.length);
        selectedDish.value = dishes.value[randomIndex];

        if (delay < 1000) {
          delay += 100; // 每次延长间隔时间，减慢速度
          rolldishes(); // 继续滚动
        } else {
          drawing.value = false; // 结束抽签
          clearTimeout(timerId!);
        }
      }, delay);
    };

    function saveDishesToLocalStorage() {
      localStorage.setItem('dishes', JSON.stringify(dishes.value));
    }

    function loadDishesFromLocalStorage() {
      const saveddishes = localStorage.getItem('dishes');
      if (saveddishes) {
        dishes.value = JSON.parse(saveddishes);
      }
    }

    return {
      backgroundImageUrl,
      dishes,
      blankDishUrl,
      selectedDish,
      showModal,
      handleAddingDish,
      startDraw,
      deleteDish,
      saveDishesToLocalStorage,

    };
  }
});
</script>

<style scoped>
/* 样式代码保持不变 */
.general_container {
  display: flex;
  flex-direction: column;
  background-size: cover;
  background-position: center;
  height: 100%;
  width: 100%;
  align-items: center;
  justify-content: center;
}

.absolute_temp_container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 40px;
  z-index: 1;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}


.save-button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #f6f7f6;
  color: rgb(12, 12, 12);
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-bottom: 10px;
}

.save-button:hover {
  background-color: #45a049;
}

.gray-note {
  color: gray; /* 灰色字体 */
  font-size: 12px; /* 较小字体 */
  margin-bottom: 10px; /* 下方留出空间 */
}
.selected-Dish {
  margin-top: 20px;
}

.dishes-grid {
  display: flex;
  gap: 20px;
  width: 80%;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
}
</style>
