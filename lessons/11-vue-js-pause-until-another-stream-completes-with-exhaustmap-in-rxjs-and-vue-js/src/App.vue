<template>
  <section class="section">
    <button class="button" :disabled="disabled$" v-stream:click="click$">{{ buttonText$ }}</button>
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
  exhaustMap,
} from "rxjs/operators";

export default {
  domStreams: ["click$", "imageError$"],
  subscriptions() {
    const createLoader = url =>
      from(this.$http.get(url)).pipe(pluck("data"));

    const luke$ = this.click$.pipe(
      mapTo("https://starwars.egghead.training/people/1"),
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
      buttonText$
    };
  }
};
</script>
