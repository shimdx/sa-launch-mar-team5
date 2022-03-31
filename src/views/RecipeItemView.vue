<script>
import {ref, watch} from 'vue'
const API_URL = "https://ksybnu5dc1.execute-api.us-east-1.amazonaws.com/video/"
const root = ref(null)
export default {
  setup(){
    const watchRecipe =watch()
  },
  props: ['id'],
  data: () => ({
    recipe: null,
    result: null,
    resultTime: null,
    resultTimeSec: null,
    queryString: null
  }),
  computed: {
    getResultTimeSec(){
      if (this.result.length>0){
        let reg = (/([0-9]{2}):([0-9]{2}):([0-9]{2}).([0-9]{3})/)
        this.resultTime = reg.exec(this.result[0].DocumentExcerpt.Text)
        console.log(this.resultTime)
        this.resultTimeSec = Number(this.resultTime[1])*60*60+Number(this.resultTime[2])*60+Number(this.resultTime[3])
        return this.resultTimeSec
      }
    },
    getResultTime(){
      if (this.result.length>0){
        let reg = (/([0-9]{2}):([0-9]{2}):([0-9]{2}).([0-9]{3})/)
        this.resultTime = reg.exec(this.result[0].DocumentExcerpt.Text)
        return this.resultTime[0]
      }
    }
  },
  methods: {
    async fetchRecipe() {
      const url = `${API_URL}${this.id}`
      console.log(url)
      this.recipe = await (await fetch(url)).json()
      console.log(this.recipe)
    },

    async fetchSearch() {
      console.log(this.queryString)
      const url = `${API_URL}${this.id}/search?q=${this.queryString}&lang=ko`
      console.log(url)
      this.result = await (await fetch(url)).json()
      console.log(this.result)
    },
    goto(index){
      console.log("move to: "+index)
      if (this.$refs.vplayer){
        this.$refs.vplayer.currentTime = index
        this.$refs.vplayer.play()
      }
    }
  },
  mounted(){
    this.fetchRecipe()
  },
}
</script>

<template>
    <section>
        <main v-if="recipe" class="bg-light">
          <div class="container">
            <div class="d-flex align-items-center p-3 my-3 text-white bg-purple rounded shadow-sm">
              <div class="lh-1">
                <h1 class="h6 mb-0 text-white lh-1">{{recipe.title}}</h1>
              </div>
            </div>
            <video id="video" ref="vplayer" controls preload="metadata" style="width:inherit;">
                <source :src="recipe.video_uri" type="video/mp4" width="100%">
                <track label="English" kind="subtitles" srclang="en" :src="recipe.captions.en">
                <track label="Korean" kind="subtitles" srclang="ko" :src="recipe.captions.ko" default>
            </video>
          </div>    
          <div class="container">          
            <div class="my-3 p-3 bg-body rounded shadow-sm">
              <h6 class="border-bottom pb-2 mb-0">  
                <svg class="bi bi-patch-question-fill" width="24" height="24" fill="currentColor" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 18 18">
                  <path d="M5.933.87a2.89 2.89 0 0 1 4.134 0l.622.638.89-.011a2.89 2.89 0 0 1 2.924 2.924l-.01.89.636.622a2.89 2.89 0 0 1 0 4.134l-.637.622.011.89a2.89 2.89 0 0 1-2.924 2.924l-.89-.01-.622.636a2.89 2.89 0 0 1-4.134 0l-.622-.637-.89.011a2.89 2.89 0 0 1-2.924-2.924l.01-.89-.636-.622a2.89 2.89 0 0 1 0-4.134l.637-.622-.011-.89a2.89 2.89 0 0 1 2.924-2.924l.89.01.622-.636zM7.002 11a1 1 0 1 0 2 0 1 1 0 0 0-2 0zm1.602-2.027c.04-.534.198-.815.846-1.26.674-.475 1.05-1.09 1.05-1.986 0-1.325-.92-2.227-2.262-2.227-1.02 0-1.792.492-2.1 1.29A1.71 1.71 0 0 0 6 5.48c0 .393.203.64.545.64.272 0 .455-.147.564-.51.158-.592.525-.915 1.074-.915.61 0 1.03.446 1.03 1.084 0 .563-.208.885-.822 1.325-.619.433-.926.914-.926 1.64v.111c0 .428.208.745.585.745.336 0 .504-.24.554-.627z"/>
                </svg>
                Ask anything about this recipe
              </h6>
              <div class="d-flex text-muted pt-3">
                  <input type="text" class="form-control" id="exampleFormControlInput1" placeholder="달걀은 언제 넣나요?" v-model="queryString" @keyup.enter="fetchSearch">
              </div>
            </div>
            <div class="my-3 p-3 bg-body rounded shadow-sm" v-if="result">
              <h6 class="border-bottom pb-2 mb-0">Suggestions</h6>
              <div class="d-flex text-muted pt-3" v-for="item in result">
                <div class="pb-3 mb-0 small lh-sm border-bottom w-100">
                  <div class="d-flex justify-content-between">
                    <p class="text-gray-dark">{{item.DocumentExcerpt.Text}}</p>
                  </div>
                  <a href="#" v-on:click="goto(getResultTimeSec)">바로가기 - {{getResultTime}}</a>
                </div>
              </div>
              <div class="d-flex text-muted pt-3" v-if="result && result.length==0">
                <div class="pb-3 mb-0 small lh-sm border-bottom w-100">
                  <div class="d-flex justify-content-between">
                    <p class="text-gray-dark">검색결과가 없습니다</p>
                  </div>
                </div>
              </div>
              <!--div class="d-flex text-muted pt-3">
                <div class="pb-3 mb-0 small lh-sm border-bottom w-100">
                  <div class="d-flex justify-content-between">
                    <p class="text-gray-dark">블라블라 쏼라쏼라 얄라리얄라</p>
                  </div>
                  <a href="#" v-on:click="goto(112)">바로가기 - 01:32</a>
                </div>
              </div>
              <div class="d-flex text-muted pt-3">
                <div class="pb-3 mb-0 small lh-sm border-bottom w-100">
                  <div class="d-flex justify-content-between">
                    <p class="text-gray-dark">블라블라 쏼라쏼라 얄라리얄라블라블라 쏼라쏼라 얄라리얄라 블라블라 쏼라쏼라 얄라리얄라</p>
                  </div>
                  <a href="#" v-on:click="goto(200)">바로가기 - 3:20</a>
                </div>
              </div-->
            </div>
          </div>
          
          <br/>

        </main>
        <footer class="container">
            <br/>
            <p>&copy; 2022 Everyone's Recipe, Inc. &middot; <a href="#">Privacy</a> &middot; <a href="#">Terms</a></p>
        </footer>
    </section>
</template>

<style scoped>

  .bg-purple {
    background-color: #8062b8;
  }
  .bd-placeholder-img {
    font-size: 1.125rem;
    text-anchor: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select: none;
  }

  @media (min-width: 768px) {
    .bd-placeholder-img-lg {
      font-size: 3.5rem;
    }
  }
</style>