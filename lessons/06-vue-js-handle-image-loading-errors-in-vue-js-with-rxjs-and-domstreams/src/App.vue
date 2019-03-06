<template>
  <section class="section">
    <button class="button" v-stream:click="click$">Click</button>
    <h1 class="title">{{ name$ }}</h1>
    <img v-stream:error="imageError$" :src="image$" alt />
  </section>
</template>

<script>
import { from, merge } from "rxjs";
import { pluck, switchMap, map, mapTo } from "rxjs/operators";

export default {
  domStreams: ["click$", "imageError$"],
  subscriptions() {
    const person$ = from(
      this.$http.get("https://starwars.egghead.training/people/1")
    ).pipe(pluck("data"));

    const luke$ = this.click$.pipe(switchMap(() => person$));

    const name$ = luke$.pipe(pluck("name"));
    const loadImage$ = luke$
      .pipe(pluck("image"))
      .pipe(map(image => `https://starwars.egghead.training/${image}`));

    const failImage$ = this.imageError$.pipe(
      mapTo(`http://via.placeholder.com/400x400`)
    );

    const image$ = merge(loadImage$, failImage$);
    return {
      name$,
      image$
    };
  }
};
</script>
