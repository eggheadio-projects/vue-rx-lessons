<template>
  <section class="section">
    <b-tabs v-model="activeTab">
      <b-tab-item v-for="person of people" :key="person.id" :label="person.name"></b-tab-item>
    </b-tabs>

    <h1 class="title">{{ name$ }}</h1>
    <img v-stream:error="imageError$" :src="image$" alt>
  </section>
</template>

<script>
import { from, merge } from "rxjs";
import {
  pluck,
  mapTo,
  switchMap,
  map,
  catchError,
  share,
  startWith,
  exhaustMap
} from "rxjs/operators";

export default {
  data() {
    return {
      activeTab: 0,
      people: [
        { name: "Luke", id: 1 },
        { name: "Darth", id: 4 },
        { name: "Leia", id: 5 },
        { name: "Yoda", id: 0 }
      ]
    };
  },

  domStreams: ["click$", "imageError$"],

  subscriptions() {
    const activeTab$ = this.$watchAsObservable("activeTab", {
      immediate: true
    }).pipe(pluck("newValue"));

    const createLoader = url => from(this.$http.get(url)).pipe(pluck("data"));

    const luke$ = activeTab$.pipe(
      map(tabId => this.people[tabId].id),
      map(id => `https://starwars.egghead.training/people/${id}`),
      exhaustMap(createLoader),
      catchError(err =>
        createLoader("https://starwars.egghead.training/people/2")
      ),
      share()
    );

    const disabled$ = merge(
      this.click$.pipe(mapTo(true)),
      luke$.pipe(mapTo(false))
    ).pipe(startWith(false));

    const buttonText$ = disabled$.pipe(
      map(bool => (bool ? "Loading..." : "Load"))
    );

    const name$ = luke$.pipe(pluck("name"));
    const loadImage$ = luke$.pipe(
      pluck("image"),
      map(image => `https://starwars.egghead.training/${image}`)
    );

    const failImage$ = this.imageError$.pipe(
      mapTo(`http://via.placeholder.com/400x400`)
    );

    const image$ = merge(loadImage$, failImage$);
    return {
      name$,
      image$,
      disabled$,
      buttonText$,
      activeTab$
    };
  }
};
</script>
