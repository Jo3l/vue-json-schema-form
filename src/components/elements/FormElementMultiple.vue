<template>
  <div :class="schema.class?schema.class:''">
    <legend v-if="schema.title" class="is-size-4 has-text-weight-bold column is-full">{{ schema.title }}</legend>
    <div v-for="(item, index) in items" class="columns" :key="index">
      <div class="column">
        <form-element :schema="schema.items" :value="item" :error="error" @input="updateValue($event, index)" :class="schema.class?schema.class:''"></form-element>
      </div>
      <div class="column is-narrow">
        <button type="button" class="button is-danger" v-if="items.length > 0" @click="items.splice(index, 1)" :key="`remove-${index}`">Quitar</button>
      </div>
    </div>
    <b-field><button type="button" class="button" @click="addItem()">Añadir</button></b-field>
  </div>
</template>

<script>
import { scaffoldFromSchema } from '@/utility/json-schema-helpers'
export default {
  name: 'FormElementMultiple',
  components: {
    // load at runtime to avoid circular dependency https://vuejs.org/v2/guide/components-edge-cases.html#Circular-References-Between-Components
    FormElement: () => import('@/components/elements/FormElement')
  },
  props: [
    'schema',
    'value',
    'error'
  ],
  data () {
    return {
      items: (this.value !== undefined) ? this.value : scaffoldFromSchema(this.schema)
    }
  },
  methods: {
    addItem() {
      this.items.push(scaffoldFromSchema(this.schema)[0])
    },
    updateValue (value, index) {
      this.items.splice(index, 1, value)
      this.$emit('input', this.items)
    }
  }
}
</script>

<style scoped>

.fieldset {
  padding-left: 2em;
  border-left: 1px solid #0C2E69;
  margin: 1.5em 0;
}

</style>
