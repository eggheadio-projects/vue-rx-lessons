<template>
  <section class="section">
    <button class="button" v-stream:click="click$">Click</button>
    <h1 class="title">{{ random$ }}</h1>
  </section>
</template>

<script>
import { Observable } from "rxjs";
import { map } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const random$ = this.click$.pipe(map(() => Math.random()));

    return {
      random$
    };
  }
};
</script>
