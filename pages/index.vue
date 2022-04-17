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
            type="submit"
            class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Search
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      searchQuery: null,
    };
  },
  methods:{
    search(){
      const requestOptions ={
        method: 'GET',
        redirect: 'follow'
      };

      const requestURL = `https://imdb-api.com/en/API/Search/${process.env.NUXT_ENV_IMDB_API_KEY}/${this.searchQuery}`;

      fetch(requestURL, requestOptions)
        .then(response => response.json())
        .then(response => this.getShowDetails(response.results[0]))
        .catch(error => alert('error', error));
    },
    getShowDetails(show){
      console.log(show);

      const requestOptions ={
        method: 'GET',
        redirect: 'follow'
      };

      const requestURL = `https://imdb-api.com/en/API/Title/${process.env.NUXT_ENV_IMDB_API_KEY}/${show.id}`;

      fetch(requestURL, requestOptions)
        .then(response => response.text())
        .then(results => console.log(results))
        .catch(error => alert('error', error));
    }
  }
}
</script>
