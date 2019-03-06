<template>
  <section class="section">
    <button class="button" v-stream:click="click$">Click</button>
    <h1 class="title">{{ name$ }}</h1>
    <img :src="image$" alt>
  </section>
</template>

<script>
import { from } from "rxjs";
import { pluck, map, switchMap } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const person$ = from(
      this.$http.get("https://starwars.egghead.training/people/1")
    ).pipe(pluck("data"));

    const luke$ = this.click$.pipe(switchMap(() => person$));

    const name$ = luke$.pipe(pluck("name"));
    const image$ = luke$.pipe(
      pluck("image"),
      map(image => `https://starwars.egghead.training/${image}`)
    );

    return {
      name$,
      image$
    };
  }
};
</script>
