<template>
  <div>
    <h1>{{ message }}</h1>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      message: ''
    };
  },
  created() {
    axios.get('http://localhost:5000/api') // update the API endpoint to reflect your deployment setup
      .then(response => {
        this.message = response.data.message;
      })
      .catch(error => {
        console.error('There was an error fetching the data!', error);
      });
  }
};
</script>

<style scoped>
/* Add your styles here */
</style>
