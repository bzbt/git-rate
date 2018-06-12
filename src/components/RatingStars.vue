<template>
  <div class="stars">
      <label v-for="i in 5" :key="i"
             class="star"
             :class="{'chosen': newRate >= i}"
             @mouseover="starOver(i)"
             @mouseout="starOut">
        <input type="radio"
               @click="setRating(i, repository.full_name)"
               :value="i">&#9733;
      </label>
  </div>
</template>

<script>
export default {
  name: 'RatingStars',

  props: ['repository', 'rated-repositories'],

  data() {
    return {
      newRate: null,
      ratedRepository: [],
      rated: false,
      items: [],
      itemRate: [],
    };
  },

  methods: {
    setRating(selectedRate, fullName) {
      if (this.rated) {
        alert('You already rated this repository');
      } else {
        this.ratedRepository.push({ fullName: fullName, itemRate: selectedRate });
        this.$emit('clicked', JSON.stringify(this.ratedRepository));

        this.rated = true;
        this.newRate = selectedRate;
      }
    },

    starOut() {
      if (!this.rated) {
        this.newRate = null;
      }
    },

    starOver(index) {
      if (!this.rated) {
        this.newRate = index;
      }
    },
  },

  mounted() {
    if (this.ratedRepositories.length > 0) {
      for (let i in this.ratedRepositories) {
        const row = JSON.parse(this.ratedRepositories[i]);

        if (row[0].fullName === this.repository.full_name) {
          this.newRate = row[0].itemRate;
          this.rated = true;
        }
      }
    }
  },
};
</script>

<style>
  .stars{
    display: inline-block;
    margin: 0 auto;
  }
  .star{
    display: inline-block;
    float:left;
    padding:3px;
    font-size:18px;
    transition: .15s all ease-in-out;
  }

  .star.chosen{
    color:#DCD10F;
  }
  .star input {
    display:none;
  }
</style>
