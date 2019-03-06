<template>
  <section class="section">
    <button class="button" v-stream:click="click$">Click</button>
    <h1 class="title">{{ luke$ }}</h1>
  </section>
</template>

<script>
import { from } from "rxjs";
import { pluck, switchMap } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const people$ = from(
      this.$http.get("https://starwars.egghead.training/people/1")
    ).pipe(pluck("data", "name"));

    const luke$ = this.click$.pipe(switchMap(() => people$));

    return {
      luke$
    };
  }
};
</script>
