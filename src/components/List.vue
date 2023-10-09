<template>
    <div 
      v-for="(list, index) in lists" 
      class="list_container" 
      @drop="onDrop($event, list)" 
      @dragover.prevent 
      @dragenter.prevent
    >
      <div class="list_container-header" :style="{background: list.headerBg}">
        <h1>{{ list.title }} <div class="length" style="color: black;">{{ list.products.length }}</div></h1>
        <select @change="sort(list)" v-model="list.selectedOption">
          <option 
            v-for="option in options" 
            :selected="option.selected" 
            :disabled="option.disabled" 
            :value="option.title"
          >
            {{ option.title }}
          </option>
        </select>
      </div>
        <ListItem :list="list" @update:inWork="addInWork" @update:changeProduct="updateProduct"/>
        <button 
          :style="{background: list.headerBg}" 
          @click="openAddModal(list)" 
          class="create_btn"
        >
          Создать  +
        </button>
    </div>
    <Modal v-if="show" :show="show" @update:show="updateShowModal">
      <textarea class="modal_input" style="height: 6vw;"  placeholder="Название" v-model="newProduct.title"></textarea>
      <textarea class="modal_input" style="height: 6vw; margin-top: 15px;" placeholder="Описание" v-model="newProduct.description"></textarea>
      <input class="modal_input" style="margin-top: 15px;" placeholder="Категория" v-model="newProduct.category">
      <input class="modal_input" type="number" style="margin-top: 15px;" placeholder="Цена" v-model="newProduct.price">
      <button class="modal_btn" @click="create()" v-if="add">Создать</button>
      <button class="modal_btn" @click="editProduct()" v-if="edit">Изменить</button>
    </Modal>
</template>

<script>
import axios from 'axios';
import ListItem from './ListItem.vue'
import Modal from './UI/Modal.vue';
export default {
  components: { ListItem, Modal },
  data() {
    return {
      show: false,
      edit: false,
      add: false,
      options: [
        {title: 'Сортировка', disabled: true, selected: true},
        {title: 'По возрастанию', disabled: false, selected: false},
        {title: 'По убыванию', disabled: false, selected: false},
      ],
      newProduct: {
        title: '',
        category: '',
        description: '',
        price: NaN,
      },
      changeProduct: '',
      changeList: '',
      lists: {
        ProductsList: {
          title: 'Необработанные',
          bg: '#FFEDD9',
          headerBg: '#FFA63F',
          selectedOption: 'Сортировка',
          edit: false,
          create: false,
          products: [],
          id: 0
        },
        inWork: {
          title: 'В работе',
          bg: '#D4E5FF',
          headerBg: '#327EEF',
          selectedOption: 'Сортировка',
          edit: false,
          create: false,
          products: [],
          id: 1
        },
        Finished: {
          title: 'Завершенные',
          bg: '#FFDCDC',
          headerBg: '#FF506F',
          selectedOption: 'Сортировка',
          edit: false,
          create: false,
          products: [],
          id: 2
        },
      },
    }
  },
  mounted() {
    axios.get('https://fakestoreapi.com/products').then(res => {
      this.lists.ProductsList.products.push(...res.data)
    })
  },
  methods: {
    onDrop(e, list) {
      const product = JSON.parse(e.dataTransfer.getData('product'));
      const previousList = JSON.parse(e.dataTransfer.getData('list'))
      const index = parseInt(e.dataTransfer.getData('index'))
      Object.keys(this.lists).forEach(key => {
        if(previousList.title === this.lists[key].title) {
          this.lists[key].products.splice(index, 1)
          console.log(index, previousList)
        }
      })
      list.products.push(product)
      list.products.sort((a, b) => b.rating.rate - a.rating.rate)
    },

    sort(list) {
      if(list.selectedOption === this.options[1].title) {
        list.products.sort((a, b) => b.rating.rate - a.rating.rate)
      } else if (list.selectedOption === this.options[2].title) {
        list.products.sort((a, b) => a.rating.rate - b.rating.rate)
      }
    },

    addInWork(product) {
      this.lists.inWork.products.push(product)
      console.log(product)
    },

    updateShowModal(value) {
      this.show = value;
      this.add = value;
      this.edit = value
    },

    openAddModal(list){
      this.changeList = list
      this.show = true,
      this.add = true
    },
    updateProduct(index, list) {
      this.changeProduct = index
      this.changeList = list
      this.newProduct = {
        title: list.products[index].title,
        description: list.products[index].description,
        category: list.products[index].category,
        price: list.products[index].price,
      }
      this.show = true,
      this.edit = true
    },

    editProduct() {
      let product = this.changeList.products[this.changeProduct]
      product.title = this.newProduct.title,
      product.description = this.newProduct.description,
      product.category = this.newProduct.category,
      product.price = this.newProduct.price,
      this.newProduct = {
        title: '',
        description: '',
        category: '',
        price: NaN
      }
      this.show = false
      this.edit = false
    },

    create() {
      if (
        this.newProduct.title.trim() !== '' &&
        this.newProduct.description.trim() !== '' &&
        this.newProduct.category.trim() !== '' &&
        this.newProduct.price
      ) {
        let id = 0;
        Object.keys(this.lists).forEach((key) => {
          id += this.lists[key].products.length;
        });

        this.changeList.products.push({
          title: this.newProduct.title,
          category: this.newProduct.category,
          description: this.newProduct.description,
          price: this.newProduct.price,
          rating: {
            rate: 0,
            count: 0,
          },
          id: id,
        });
        this.show = false;
        this.add = false;
      } else {
        alert('Все поля должны быть заполнены');
      }
      this.newProduct = {
        title: '',
        description: '',
        category: '',
        price: NaN,
      };
    }
  }
}
</script>

<style>

.list_container {
  height: fit-content;
  min-height: 15vw;
  max-height: 45vw;
  width: 25vw;
  border-radius: 1vw;
  display: flex;
  flex-direction: column;
  margin-top: 15px;
}


h1 {
  display: flex;
  align-items: center;
  font-weight: 500;
  font-size: 1vw;
}

.length {
  margin-left: 15px;
  background-color: white;
  border-radius: 50%;
  width: 1.5vw;
  height: 1.5vw;
  color: black;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  font-size: 0.8vw;
}

.list_container-header {
  font-size: 0.5vw;
  width: inherit;
  flex: 0 0 2.5vw;
  border-radius: 1vw 1vw 0 0;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-around;
}

select {
  width: 7vw;
  height: 2vw;
  padding: 0.2vw;
  border-radius: 2vw;
  border:  0.2vw solid #FFFFFF;
  background: none;
  font-size: 0.6vw;
  outline: none;
}

option {
  color: black;
}

.modal_input {
  height: 3vw;
  align-self: center;
  width: 25vw;
  padding: 1vw;
  border: 1 solid black;
  color: black;
  border-radius: 1vw;
  word-wrap: break-word;
  resize: none;
  outline: none;
}

.modal_btn {
  width: 10vw;
  height: 3vw;
  background-color: #327EEF;
  border: none;
  border-radius: 1vw;
  margin-top: 1vw;
  align-self: center;
  cursor: pointer;
}

.create_btn {
  align-self: flex-end;
  width: 8vw;
  flex: 0 0 3vw;
  border-radius: 0 0 2vw 2vw;
  cursor: pointer;
  border: none;
  text-align: center;
  position: relative;
  left: -1.4vw;
  font-size: 1vw;
  font-weight: medium;
}


</style>