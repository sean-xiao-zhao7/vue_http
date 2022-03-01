<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="load">Load Submitted Experiences</base-button>
      </div>
      <p v-if="loading">Loading</p>
      <ul v-else-if="!loading && loadedResults && loadedResults.length > 0">
        <survey-result
          v-for="result in loadedResults"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
      <p v-else>No experiences.</p>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      loadedResults: [],
      loading: false,
      errors: [],
    };
  },
  methods: {
    load() {
      this.loading = true;
      fetch('https://vue1-f45fd-default-rtdb.firebaseio.com/surveys.json', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      })
        .then((res) => {
          if (res.ok) {
            return res.json();
          } else {
            throw new Error('Error!');
          }
        })
        .then((data) => {
          for (const id in data) {
            this.loadedResults.push({
              id: id,
              name: data[id]['name'],
              rating: data[id]['rating'],
            });
          }
          this.loading = false;
        })
        .catch((error) => {
          this.errors.push(`Failed. ${error.message}`);
        });
    },
  },
  mounted() {
    this.load();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
