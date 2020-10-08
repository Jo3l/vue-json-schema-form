<template>
  <b-field 
  :label="schema.title" 
  :message="error.message?error.message:schema.description" 
  :type="error.message?'is-danger':''" 
  :class="schema.class?schema.class:''">
    <b-input :disabled="schema.disabled" :value="value" @blur="blur($event)" @input="$emit('input', $event)" :placeholder="schema.example"></b-input>
  </b-field>
</template>

<script>
export default {
  name: 'TextElement',
  props: [
    'schema',
    'value',
    'error'
  ],
  data () {
  return {
  }
  },
  methods: {
    blur (event) {
      this.$emit('input', event.target.value)
    }
  }
}
</script>
