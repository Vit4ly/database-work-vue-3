<template>
  <div v-if="people.length !== 0">
    <div class="card inline"
         v-for="{firstName, id} in people"
         :key="id"
    >
      <h3>{{ firstName }}</h3>
      <button class="btn" @click="$emit('remove', id)">Удалить</button>
    </div>
  </div>
  <div class="card center" v-else>
    <h4>Список пуст</h4>
    <button
        @click="$emit('load-people')"
        class="btn"

    >
      Загрузить человека
    </button>
  </div>


</template>

<script>
export default {
  props: {
    people: Array
  },
  emits: ['load-people', 'remove'],
  name: "Load"
}
</script>

<style scoped>
.inline {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
