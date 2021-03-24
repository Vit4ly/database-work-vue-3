<template>
  <div class="container">
    <app-alert
        :alert="alert"
        @close="alert = null"
    ></app-alert>
    <form class="card" @submit.prevent="createPeople">
      <h2>Работа с базой данных</h2>
      <div class="form-control ">
        <label>Введите имя</label>
        <input
            type="text"
            class="input"
            v-model="name"
        >
      </div>
      <button type="submit" class="btn " :disabled="!name.length">Создать человека</button>

    </form>

    <app-loader v-if="loading"></app-loader>

    <Load
        v-else
        :people="people"
        @loadPeople="loadPeople"
        @remove="removePerson"
    ></Load>
  </div>
</template>

<script>

import Load from "@/Load";
import AppAlert from "@/AppAlert";
import AppLoader from "@/AppLoader";
import axios from 'axios';

export default {

  data() {
    return {
      name: '',
      people: [],
      alert: null,
      loading: false
    }
  },
  mounted() {
    this.loadPeople()
  },
  methods: {
    async createPeople() {
      const response = await fetch('https://vue3-data-base-http-default-rtdb.firebaseio.com/people.json', {
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
      try {
        this.loading = true
        const {data} = await axios.get('https://vue3-data-base-http-default-rtdb.firebaseio.com/people.json')
        this.people = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
        this.loading = false
      } catch (e) {
        this.alert = {
          type: 'danger',
          title: 'Ошибка',
          text: e.message
        }
        this.loading = false
      }

    },
    async removePerson(id) {
      try {
        const person = this.people.find(person => person.id === id).firstName
        await axios.delete(`https://vue3-data-base-http-default-rtdb.firebaseio.com/people/${id}.json`)
        this.people = this.people.filter(person => person.id !== id)
        this.alert = {
          type: 'primary',
          title: 'Успешно',
          text: `Пользователь c именем ${person} был удален`
        }
      } catch (e) {

      }
    }
  },
  components: {
    Load,
    AppAlert,
    AppLoader
  }
}
</script>

<style>

</style>
