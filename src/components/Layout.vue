<script setup>
import Button from "./ui/Button.vue";
import Loader from "./ui/Loader.vue";
import { ref,watch,onMounted } from "vue";

const postsApi = ref(null);
const loader = ref(false);
const cards = ref([]);
const moreUsers = ref(50);
const trash = ref([]);
const trashContainer = ref(false);
const userInfo = ref(false);
const objectDeteile = ref(null);
console.log(moreUsers);

async function AddUser(qwery) {
  loader.value = true;
  const data = await fetch(`https://randomuser.me/api/?results=${qwery}`);
  const res = await data.json();
  res.results.forEach((el) => cards.value.push(el));
  loader.value = false;
  console.log(postsApi);
  console.log(res);
}


const removeUser = () => {
  cards.value.pop();
};

const loadMore = () => {
  AddUser(moreUsers.value);
};

const removeAll = () => {
  cards.value.splice(1);
};

const removeToTrash = (card) => {
  cards.value = cards.value.filter((t) => t !== card);
  trash.value.push(card);
  console.log(trash);
};

const removeFromTrash = (card) => {
  trash.value = trash.value.filter((t) => t !== card);
  if(trash.value.length < 1) {
    trashContainer.value = false
  }
};

const userDeteile = (card) => {
  userInfo.value = true;
  objectDeteile.value = card;
};

const restore = (deleteUser) => {
cards.value.push(deleteUser)
trash.value = trash.value.filter((t) => t !== deleteUser);
}

watch(
  cards,(newVal) => {localStorage.setItem("cards", JSON.stringify(newVal));},{deep: true,});
watch(
  trash,(newVal2) => {localStorage.setItem("trash", JSON.stringify(newVal2));},{deep: true,});
  
onMounted(() => {cards.value = JSON.parse(localStorage.getItem("cards")) || [];});
onMounted(() => {trash.value = JSON.parse(localStorage.getItem("trash")) || [];});

</script>
<template>
  <div class="container">
    <div class="navigation">
      <div class="navigation_container">
        <div class="navigation_container--button">
          <Button class="btn" @click="AddUser">Добавить</Button>
        </div>

        <div class="navigation_container--button">
          <Button class="btn" @click="removeUser">Удалить</Button>
        </div>
        <div class="navigation_container--button">
          <Button class="btn" @click="loadMore">Заполниить</Button>
        </div>
        <div class="navigation_container--button">
          <Button class="btn" @click="removeAll">Очистить</Button>
        </div>
        <div class="navigation_container--button">
          <Button class="btn-trash" @click="trashContainer = true">
            <p v-if="trash.length > 0" class="trash-length">
              {{ trash.length }}
            </p>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="trash"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0"
              />
            </svg>
            Kорзина
          </Button>
        </div>
      </div>
    </div>
    <div class="card-container">
      <div class="card" v-for="card in cards" :key="card">
        <h2 class="card__title">
          {{ card.name.title + " " + card.name.first + " " + card.name.last }}
        </h2>
        <h3 class="card__country">{{ card.location.country }}</h3>
        <h4 class="card__city">{{ card.location.city }}</h4>
        <button class="desc-btn" @click="userDeteile(card)">подробнее</button>
        <div class="container_btn-del" @click="removeToTrash(card)">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
            fill="currentColor"
            class="btn-del"
          >
            <path
              d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z"
            />
          </svg>
        </div>
      </div>
    </div>
    <div v-if="trashContainer">
      <div class="deleteUsers__baground"></div>
      <div class="deleteUsers">
        <div class="deleteUsers__closeBtn" @click="trashContainer = false">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
            fill="currentColor"
            class="btn-del"
          >
            <path
              d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z"
            />
          </svg>
        </div>
        <div class="card" v-for="deleteUser in trash" :key="deleteUser">
          <h2 class="card__title">
            {{deleteUser.name.title +" " + deleteUser.name.first +" " + deleteUser.name.last}}
          </h2>
          <h3 class="card__country">{{ deleteUser.location.country }}</h3>
          <h4 class="card__city">city:{{ deleteUser.location.city }}</h4>
          <button class="desc-btn" @click="restore(deleteUser)">восстановить</button>
          <button class="desc-btn" @click="removeFromTrash(deleteUser)">удалить</button>
        </div>
      </div>
    </div>
    <div v-if="userInfo">
      <div class="deleteUsers__baground"></div>
      <div class="userInfo">
        <div class="deleteUsers__closeBtn" @click="userInfo = false">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
            fill="currentColor"
            class="btn-del"
          >
            <path
              d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z"
            />
          </svg>
        </div>
        <div class="card">
          <h2 class="card__title">
            {{objectDeteile.name.title +" " + objectDeteile.name.first +" " + objectDeteile.name.last}}
          </h2>
          <img class="deteilPicture" :src="objectDeteile.picture.large" />
          <h3 class="card__country">
            <span class="detailInfoUser">Country:</span>
            {{ objectDeteile.location.country }}
          </h3>
          <h4 class="card__city card__city--detail">
            <span class="detailInfoUser">City:</span>
            {{ objectDeteile.location.city }}
          </h4>
          <h4 class="card__city card__city--detail">
            <span class="detailInfoUser">Email:</span>
            {{ objectDeteile.email }}
          </h4>
        </div>
      </div>
    </div>
    <Loader v-if="loader"/>
  </div>
</template>

<style scoped>

</style>