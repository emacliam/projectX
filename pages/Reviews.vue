<template>
  <main>

  <div class="bg-white max-w-md mx-auto my-8 border border-grey-light rounded-t-lg overflow-hidden" v-for="comment in comments" :key="comment._id" >
      <div class="px-2 pt-2 flex-grow">
        <div v-for="product in products" :key="product._id">

          <div v-if="product._id == comment.productId">
<div>
          <p class="text-lg font-bold">Message</p>
          <p>{{comment.user.name}}</p>
        </div>

        <template v-if="comment.reply">
           <p class="bg-green-300 rounded-lg w-24 text-center">Replied</p>
            <footer class="border-t border-grey-lighter text-sm flex flex-col my-2" >
              <p class="text-lg font-bold underline">reply</p>
              <div>
                <p>{{comment.reply}}</p>

              </div>


        </footer>
        </template>
         <template v-else>
            <p class="bg-red-300 rounded-lg w-24 text-center">Unreplied</p>
          <footer class="border-t border-grey-lighter text-sm flex" >

          <div class="flex">
               <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
              class="feather feather-message-circle h-6 w-6 mr-1">
              <path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5z"></path>
            </svg>
            <span>Reply</span>
          </div>
          <a href="#" class="block no-underline text-black flex px-4 py-2 items-center hover:bg-grey-lighter">
              <div class="w-full md:w-full px-3 mb-2 mt-2 flex items-center">
            <textarea class="bg-gray-100 rounded border border-gray-400 leading-normal resize-none w-full h-12 py-2 px-3 font-medium placeholder-gray-700 focus:outline-none focus:bg-white" name="body" placeholder='Reply to comment' v-model="replay"></textarea>
             <div class="-mr-1">
               <span class="bg-teal-600 font-medium py-1 px-4 rounded-lg ml-2 tracking-wide mr-1 hover:bg-black hover:text-white" @click="reply(comment._id)">Post</span>
            </div>
         </div>
          </a>
        </footer>
        </template>
          </div>

        </div>


      </div>
</div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      comments:'',
      replay:'',
      products:''
    }
  },
   async asyncData ({ $axios, params }) {
    try {
      const response = await $axios.$get('/api/products')
      const comment = await $axios.$get('/api/comments')

       let [productResponse, commentResponse] = await Promise.all([
        response,
        comment
      ])


      return {
        products: productResponse.products,
        comments: commentResponse.comments
      }
    } catch (error) {
      console.log(error)
    }
  },
  methods: {
     async reply(id){
     this.$nuxt.$loading.start()
    try {
      let data ={
        reply:this.replay
      }
    const response = await this.$axios.$post(`/api/comments/${id}`,data)
    if(response.success){
    const data = await this.$options.asyncData(this.$root.$options.context)
    console.log(data)
     this.$toast.success('Reply send').goAway(4000);
        this.$nuxt.$loading.finish()
        this.replay = ''
    }else{
       this.$nuxt.$loading.finish()
    this.$toast.error("Sorry try again later").goAway(4000);
    this.replay = ''
    }

    } catch (err) {
        console.log(err)
    }

  }
  },

}
</script>

<style>

</style>