<template>
  <div class="fieldSet" style="margin-bottom:1em;">
    <legend v-if="schema.title" class="is-size-4 has-text-weight-bold is-full">{{ schema.title }}</legend>
    <div class="content" v-if="schema.description">{{ schema.description }}</div>
    <div :class="schema.class">
      <form-element 
        v-for="(child, key) in schema.properties" 
        :schema="child" 
        :error="error[key]?error[key]:{message:''}" 
        :value="internalValue[key]" 
        :key="key" 
        @input="updateValue($event, key)"
      >
      </form-element>
    </div>
  </div>
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
  },
  mounted: function(){
    this.updateValue()
  }
}
</script>

