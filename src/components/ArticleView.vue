<script setup>
import { ref } from 'vue';
import { RouterView } from 'vue-router'

const author = ref('');
const date = ref(new Date().toISOString().slice(0,10));
const text = ref('');

const article = ref({});


const saveArticle = () => {
      fetch('https://api-projekt-mmd-default-rtdb.europe-west1.firebasedatabase.app/.json', {
        method: 'POST',
        body: JSON.stringify({
          author: author.value,
          date: date.value,
          text: text.value,
        })
      })
      .catch(error => {
          console.error('Artiklen blev ikke lagt op', error);
      })
      console.log(author.value)
      console.log(date.value)
      console.log(text.value)
      author.value = '';
      date.value = new Date().toISOString().slice(0,10);
      text.value = '';
    };
const showArticle = () => {
      fetch('https://api-projekt-mmd-default-rtdb.europe-west1.firebasedatabase.app/.json', {
        method: 'GET',
      })
      .then((rawResponse) => {
        return rawResponse.json();
      })
      .then((response) => { 
        console.log(response);
        article.value = response;
      })
      .catch(error => {
          console.error('Artiklerne blev ikke hentet', error);
      })
    };
let timer = setInterval(showArticle, 3000)
showArticle();

const deleteArtikel = () => {
  fetch('https://api-projekt-mmd-default-rtdb.europe-west1.firebasedatabase.app/.json', {
    method: 'GET',
  })
 .then((rawResponse) => {
    return rawResponse.json();
  })
 .then((response) => {
    const keys = Object.keys(response);
    const latestKey = keys[keys.length - 1];
    fetch(`https://api-projekt-mmd-default-rtdb.europe-west1.firebasedatabase.app/${latestKey}.json`, {
      method: 'DELETE',
    })
   .catch(error => {
      console.error('Artiklen blev ikke slettet', error);
    })
  })
 .catch(error => {
    console.error('Artiklerne blev ikke hentet', error);
  })
};
console.log("ArticleView virker");
</script>

<template>
  <h1 class="quahoggazette">Quahog Gazette</h1>
  <p class="editor">Lead editor: Tom Tucker</p>
  <p class="tuckerquote">"Good evening, i'm Tom Tucker."</p>
  <div class="artikel-skriv">
    <form v-on:submit.prevent="saveArticle">
      <input class="forfatter-felt" type="text" v-model="author" placeholder="Forfatter" />
      <input class="forfatter-felt" type="text" v-model="date" placeholder="Dato" disabled />
      <textarea class="artikeltekst-felt" v-model="text" placeholder="Artikeltekst"></textarea>
      <button type="submit" class="indsendknap">Indsend Artikel</button>
    </form>
  </div>
  <div class="artikelvisning">
    <ul>
      <li v-for="(article, id) in article" :key="id" class="liste">
       <h2 class="forfatteroverskrift">{{ article.author }}</h2>
       <p class="datoerne">{{ article.date }}</p>
       <p class="teksterne">{{ article.text }}</p>
      </li>
    </ul>
  </div>
  <div class="knapslet">
  <button @click="deleteArtikel" class="sletteknap">Slet Seneste Artikel</button>
  </div>
</template>