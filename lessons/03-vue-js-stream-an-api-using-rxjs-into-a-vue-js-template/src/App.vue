<template>
  <section class="section">
    <button class="button" v-stream:click="click$">Click</button>
    <h1 class="title">{{ people$ }}</h1>
  </section>
</template>

<script>
import { from } from "rxjs";
import { map, pluck } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const people$ = from(
      this.$http.get("https://starwars.egghead.training/people/1")
    ).pipe(pluck("data", "name"));

    const random$ = this.click$.pipe(map(() => Math.random()));

    return {
      random$,
      people$
    };
  }
};
</script>
