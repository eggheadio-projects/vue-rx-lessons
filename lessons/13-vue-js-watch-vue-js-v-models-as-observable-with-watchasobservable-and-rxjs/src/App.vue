<template>
  <section class="section">
    {{ activeTab$ }}
    <b-tabs v-model="activeTab">
      <b-tab-item label="Luke"></b-tab-item>
      <b-tab-item label="Darth"></b-tab-item>
      <b-tab-item label="Leia"></b-tab-item>
    </b-tabs>

    <button
      class="button"
      :disabled="disabled$"
      v-stream:click="{ subject: click$, data: 1 }"
    >{{ buttonText$ }}</button>
    <button
      class="button"
      :disabled="disabled$"
      v-stream:click="{ subject: click$, data: 4 }"
    >{{ buttonText$ }}</button>
    <button
      class="button"
      :disabled="disabled$"
      v-stream:click="{ subject: click$, data: 5 }"
    >{{ buttonText$ }}</button>
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
      activeTab: 0
    };
  },
  domStreams: ["click$", "imageError$"],
  subscriptions() {
    const activeTab$ = this.$watchAsObservable("activeTab", {
      immediate: true
    }).pipe(pluck("newValue"));

    const createLoader = url => from(this.$http.get(url)).pipe(pluck("data"));

    const luke$ = this.click$.pipe(
      pluck("data"),
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
