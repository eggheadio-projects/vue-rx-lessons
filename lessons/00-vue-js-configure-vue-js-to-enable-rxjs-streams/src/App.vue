<template>
  <section>
    <h1>{{interval$}}</h1>
  </section>
</template>

<script>
import { interval } from 'rxjs'

export default {
  subscriptions() {
    return {
      interval$: interval(1000)
    }
  }
};
</script>
