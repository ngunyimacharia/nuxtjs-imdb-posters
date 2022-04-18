<template>
  <div class="bg-gray-100 min-h-screen min-w-screen">

    <div class="max-w-3xl mx-auto py-12 sm:px-6 lg:px-8">
      <form @submit.prevent="search" class="bg-white shadow sm:rounded-lg">
        <div class="px-4 py-5 sm:p-6">
          <label for="search" class="block text-sm font-medium text-gray-700">Search</label>
          <div class="mt-1">
            <input
              v-model="searchQuery"
              type="text"
              name="search"
              id="search"
              class="shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full sm:text-sm border-gray-300 rounded-md"
              placeholder="Search for movie..."
            />
          </div>
        </div>
        <div class="text-center p-2">
          <button
            v-if="!loading"
            type="submit"
            class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            {{searchQuery ? "Search" : "Random"}}
          </button>
          <p v-if="loading" class="m-2 italic text-gray-400">
            {{loading}}
          </p>
        </div>
      </form>
    </div>

    <div class="text-center">
      <img
        :alt="`${show.title} poster`"
        v-if="show"
        class="mx-auto my-10 w-200 h-200"
        :src="posterURL"
        referrerpolicy="no-referrer"
        @load="loading = null"
        @error="search"
      />
    </div>

  </div>
</template>

<script>
export default {
  data(){
    return {
      loading:false,
      searchQuery: null,
      show:null,
    };
  },
  mounted(){
    this.search();
  },
  computed:{
    posterURL(){
      if(!this.show){
        return;
      }
      return this.$cloudinary.image.fetchRemote(
        `https://image.tmdb.org/t/p/original/${this.show.backdrop_path}`,
        {
          width:800,
          height:800,
          crop:"fit",
          transformation: [
            {
              color:"#" + Math.floor(Math.random()*16777215).toString(16),
              overlay: {
                font_family: "Ultra",
                font_weight: "bold",
                font_size: 120,
                text: this.show.title
              }
            },
            {flags: "layer_apply", gravity: "south", y: 100}
          ]
        }
      );
    }
  },
  watch:{
    show(val){
      console.log(val);
    }
  },
  methods:{
    search(){
      Array.prototype.random = function () {
        return this[Math.floor((Math.random()*this.length))];
      }

      this.loading = "Getting search results...";
      const requestOptions ={
        method: 'GET',
        redirect: 'follow'
      };

      const requestURL = "https://api.themoviedb.org/3/search/movie?";

      const alphabet = "abcdefghijklmnopqrstuvwxyz";

      const queryParams = new URLSearchParams({
          api_key: process.env.NUXT_ENV_TMDB_API_KEY,
          query: this.searchQuery ?? alphabet[Math.floor(Math.random() * alphabet.length)],
      });

      fetch(requestURL + queryParams, requestOptions)
        .then(response => response.json())
        .then(
          response => this.show = response
                                        .results
                                        .filter(show => show.backdrop_path)
                                        .random()
            )
          .then(() => this.loading="Loading image..")
        .catch(error => alert('Error:' + error));
    },
  }
}
</script>
