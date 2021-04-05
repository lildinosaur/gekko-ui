<template>
  <q-page padding>
    <div class="text-h4 q-mb-md">Strategies</div>
    <list></list>
    <editor></editor>
  </q-page>
</template>

<script>
  import editor from '../components/strategies/stratEditor'
  import list from '../components/strategies/stratList'
  export default {
      name: 'StrategiesPage',
      components: {
        editor,
        list
    }
  }
</script>