<template>
  <section class="section">
    <h1 class="title">{{ timesTwo$ }}</h1>
    <h1 class="title">{{ timesThree$ }}</h1>
  </section>
</template>

<script>
import { interval } from "rxjs";
import { map } from "rxjs/operators";

export default {
  subscriptions() {
    const interval$ = interval(1000);

    const timesTwo$ = interval$.pipe(map(i => i * 2));
    const timesThree$ = interval$.pipe(map(i => i * 3));
    return {
      timesTwo$,
      timesThree$
    };
  }
};
</script>
