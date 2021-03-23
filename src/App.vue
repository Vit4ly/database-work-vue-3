<template>
  <div class="container">
    <div class="card">
      <h2>Работа с базой данных</h2>
      <form action="" class="form-control " @submit.prevent="createPeople">
        <label>Введите имя</label>
        <input
            type="text"
            class="input"
            v-model="name"
        >
        <div class="pt-1">
          <button type="submit" class="btn " :disabled="!name.length">Создать человека</button>
        </div>

      </form>

    </div>
    <Load
        :people="people"
        @loadPeople="loadPeople"
        @delete="deletePeople"
    ></Load>
  </div>
</template>

<script>

//https://vue-with-http-4fce4-default-rtdb.firebaseio.com/json
import Load from "@/Load";
import axios from 'axios';

export default {

  data() {
    return {
      name: '',
      people: []
    }
  },
  mounted() {
    this.loadPeople()
  },
  methods: {
    async createPeople() {
      const response = await fetch('https://vue-with-http-4fce4-default-rtdb.firebaseio.com/people.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application.json'
        },
        body: JSON.stringify({
          firstName: this.name
        }),

      })
      const firebaseData = await response.json()
      this.people.push({
        firstName: this.name,
        id: firebaseData.name
      })
      this.name = ''

    },
   async loadPeople() {
     const { data } = await axios.get('https://vue-with-http-4fce4-default-rtdb.firebaseio.com/people.json')
     this.people = Object.keys(data).map(key => {
       return {
         id: key,
         ...data[key]
       }
     })
    },
    deletePeople(id) {
      console.log(id)
    }
  },
  components: {
    Load
  }
}
</script>

<style>

</style>
