<template>
    <div class="modal">
        <div class="modal_container" ref="modal">
          <slot></slot>
        </div>
    </div>
</template>

<script>
import { ref } from 'vue';
import { onClickOutside } from '@vueuse/core';

export default {
  props: ['show'],
  
  setup(_, { emit }) {
    const modal = ref(null);

    onClickOutside(modal, () => {
      emit('update:show', false);
    });

    return { modal };
  }
}
</script>

<style scoped>
.modal {
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  position: fixed;
  z-index: 2;
  top: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal_container {
  width: 40vw;
  height: 30vw;
  background-color: white;
  padding: 20px;
  z-index: 24;
  position: absolute;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  border-radius: 1vw;
}
</style>