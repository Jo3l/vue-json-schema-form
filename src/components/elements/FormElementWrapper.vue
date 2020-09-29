<template>
  <fieldset :class="'fieldSet '+schema.class?schema.class:'field'">
    <legend v-if="schema.title" class="is-size-4 has-text-weight-bold">{{ schema.title }}</legend>
    <div class="content" v-if="schema.description">{{ schema.description }}</div>
    <form-element v-for="(child, key) in schema.properties" :schema="child" :error="error" :value="internalValue[key]" :key="key" @input="updateValue($event, key)"></form-element>
  </fieldset>
</template>

<script>
// import FormElement from '@/components/elements/FormElement'
export default {
  name: 'FormElementWrapper',
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
      internalValue: (this.value !== undefined) ? this.value : {}
    }
  },
  watch: {
    value(val) {
      this.internalValue = val
    }
  },
  methods: {
    updateValue (value, child) {
      this.internalValue[child] = value
      this.$emit('input', this.internalValue)
    }
  }
}
</script>

