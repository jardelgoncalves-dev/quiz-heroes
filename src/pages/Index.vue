<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      heroes: [],
      heroesSelecteds: [],
      clicked: false,
    };
  },
  methods: {
    setupTurn() {
      this.heroesSelecteds = [];
      this.selectHeroesRandom();
      this.selectHeroesRandom();
      this.selectHeroesRandom();
      const randomIndex = Math.floor(Math.random() * 2);
      this.heroesSelecteds[randomIndex].rightOne = true;
    },
    selectHeroesRandom() {
      const randomIndex = Math.floor(Math.random() * this.heroes.length);
      const [selected] = this.heroes.splice(randomIndex, 1);
      this.heroesSelecteds.push(selected);
    },
    answer(hero) {
      this.clicked = true;
      if (hero.rightOne) {
        this.$q.notify({
          color: 'green',
          message: 'Acertou mizera',
          position: 'bottom',
        });
      } else {
        this.$q.notify({
          color: 'red',
          message: 'Errou disgraçaaaaaaaa',
          position: 'bottom',
        });
      }
      setTimeout(() => {
        this.setupTurn();
        this.clicked = false;
      }, 1000);
    },
  },
  computed: {
    theRightOne() {
      return this.heroesSelecteds.find((hero) => hero.rightOne) || {};
    },
  },
  created() {
    const url = 'https://cdn.jsdelivr.net/gh/akabab/superhero-api@0.3.0/api/all.json';
    this.$axios.get(url)
      .then((response) => response.data)
      .then((heroes) => {
        this.heroes = heroes.map((hero) => ({ ...hero, rightOne: false }));
        this.setupTurn();
      });
  },
};

</script>

<template>
  <q-page class="flex flex-center">
    <div style="display: flex;justify-content: center;" v-if="theRightOne.images">
      <q-card class="my-card">
        <q-card-section>
          <q-img
            :src="theRightOne.images.sm"
            style="height: 280px; width: 320px; object-fit: cover;"
          />
        </q-card-section>

        <q-separator />

        <q-card-actions vertical>
          <q-btn
            v-for="hero in heroesSelecteds" :key="hero.id"
            color="primary"
            :label="hero.name"
            :disable="clicked"
            size="lg"
            @click="answer(hero)"
          />
        </q-card-actions>
      </q-card>
    </div>
  </q-page>
</template>
