<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading Data</p>
      <p v-else-if="!isLoading && error">{{error}}</p>
      <p v-else-if="!loading && (!results || results.length === 0)"> No survey results found start adding some survey results.</p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return {
      results: [],
      isLoading: false,
      error: null
    }
  },
  methods: {
    loadExperiences(){
        this.isLoading = true;
        this.error = null;
        fetch('https://vue-http-demo-10e57-default-rtdb.firebaseio.com/survey.json')
          .then((res) => {
            if(res.status === 200){
              return res.json();
            }
          })
          .then((data) => {
            this.isLoading = false;
            console.log('actual data', data);
            const results = [];
            for (const id in data) {
              results.push({
                id: id,
                name: data[id].name,
                rating: data[id].rating
              });
            }
            this.results = [...results];
          })
          .catch((err) => {
            this.isLoading = false;
            this.error = 'Failed to fetch data. Please try after some time';
            console.log(err);
          })
    }
  },
  mounted() {
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>