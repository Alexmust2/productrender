<template>
    <ul :style="{background: list.bg}">
        <li 
            v-for="(product, index) in list.products" 
            draggable="true" 
            @dragstart="onDragStart($event,index,list, product)" 
            class="card_container" 
            :style="{background: list.headerBg}"
        > 
            <img :src="product.image" class="product_image">
            <li><b>Название: </b> {{ product.title }}</li>
            <li><b>Категория: </b>{{ product.category }}</li>
            <li><b>Описание: </b>{{ product.description }}</li>
            <li><b>Цена: </b>{{ product.price }}$</li>
            <li><b>Рейтинг: </b>{{ product.rating.rate }} <img class="rate" src="../assets/star.png"></li>
            <li><b>Отзывы: </b>{{ product.rating.count }}</li>
            <li><b>Id: </b>{{ product.id }}</li>
            <div class="btns">
                <img class="del_btn" src="../assets/delete.png" @click="del(list, index)">
                <img class="edit_btn" style="margin-left: 1vw;" src="../assets/edit.png" @click="sendIndex(index, list)">
            </div>
            <button 
                v-if="list.id === 0" 
                class="move_card-btn" 
                @click="moveCard(product, list, index)"
            >
                Взять в работу
            </button>
        </li>
    </ul>
</template>

<script>
export default {
  props: ['list'],
  methods: {
    onDragStart(e,index,list, product) {
        e.dataTransfer.dropEffect = 'move'
        e.dataTransfer.effetAllowed = 'move'
        e.dataTransfer.setData('product', JSON.stringify(product))
        e.dataTransfer.setData('index', index.toString())
        e.dataTransfer.setData('list', JSON.stringify(list))
    },

    del(list, index) {
        list.products.splice(index, 1)
    },

    sendIndex(index, list) {
        this.$emit('update:changeProduct', index, list)
    },
    
    moveCard(product, list, index) {
        this.$emit('update:inWork', product)
        list.products.splice(index, 1)
    }
  }
}
</script>

<style scoped>
ul {
    min-height: 20vw;
    height: fit-content;
    width: inherit;
    border-radius: 0 0 1vw 1vw;
    overflow: auto;
}

ul::-webkit-scrollbar {
  width: 0.5rem;
  background-color: transparent;
}

ul::-webkit-scrollbar-thumb {
  background-color: transparent;
}

ul::-webkit-scrollbar-track {
  background-color: transparent;
}

.card_container {
    border-radius: 1vw;
    margin-left: 0.5vw;
    list-style: none;
    padding: 1vw;
    background: white;
    margin: 1vw;
    display: flex;
    flex-direction: column;
}

li {
    font-size: 1vw;
    margin-top: 1vw;
    font-weight: 500;
}

img {
  width: 4vw;
  align-self: center;
}

.btns {
    align-self: flex-end;
}

.move_card-btn {
    align-self: center;
    width: 15vw;
    height: 2.5vw;
    border-radius: 1vw;
    border: none;
    background: #327EEF;
    font-size: 1vw;
    font-weight: 800;
    margin-top: 1vw;
    cursor: pointer;
}

button {
    width: 2vw;
    height: 2vh;
    font-size: 0.5vw;
}

.del_btn, .edit_btn {
    width: 1vw;
    cursor: pointer;
}

.rate {
    width: 0.8vw;
}

</style>