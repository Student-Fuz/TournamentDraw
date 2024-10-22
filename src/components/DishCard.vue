<template>
    <div class="dish-card" @dblclick="$emit('remove')">
      <img :src="picUrl" alt="avatar" class="player-avatar" />
      <h3 class="player-name">{{ name }}</h3>
      <p class="player-slogan">{{ slogan }}</p>
    </div>
</template>
  
<script lang="ts">
import { defineComponent } from 'vue';
import type { PropType } from 'vue';

export default defineComponent({
name: 'DishCard',
props: {
    picUrl: {
    type: String as PropType<string>,
    required: true
    },
    name: {
    type: String as PropType<string>,
    required: true
    },
    slogan: {
    type: String as PropType<string>,
    required: true
    },  
}



});
</script>

<style scoped>
.dish-card {
border: 1px solid #ddd;
border-radius: 8px;
padding: 16px;
text-align: center;
width: 160px;
height: 200px;
box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.player-avatar {
width: 100px;
height: 100px;
border-radius: 50%;
object-fit: cover;
}

.player-name {
font-size: 1.2em;
margin-top: 10px;
color: #333;
}

.player-slogan {
font-size: 0.9em;
margin-top: 8px;
color: #666;
}
</style>
  