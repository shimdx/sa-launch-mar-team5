<script>
const API_URL = "https://ksybnu5dc1.execute-api.us-east-1.amazonaws.com/video/"
export default {
  props: ['id'],
  data: () => ({
    recipe: null
  }),
  methods: {
    async fetchRecipe() {
      const url = `${API_URL}${this.id}`
      console.log(url)
      this.recipe = await (await fetch(url)).json()
      console.log(this.recipe)
    },
  },
  mounted(){
    this.fetchRecipe()
  }
}
</script>

<template>
    <div class="col">
        <div class="card shadow-sm" v-if="recipe">
        <img class="bd-placeholder-img card-img-top" width="100%" height="225" :src="recipe.image">
        <div class="card-body">
            <p class="card-text"><b>{{recipe.title}}</b></p>
            <p class="card-text">{{recipe.description}}</p>
            <div class="d-flex justify-content-between align-items-center">
            <div class="btn-group">
                <router-link :to="{name:'recipeItem', params: {id: id}}" class="btn btn-sm btn-outline-secondary">View</router-link>
            </div>
            <small class="text-muted">{{recipe.duration}} mins</small>
            </div>
        </div>
        </div>
    </div>
</template>