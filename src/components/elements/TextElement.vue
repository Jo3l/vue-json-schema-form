<template>
  <b-field :label="schema.title" :message="error?error:schema.description" :type="error?'is-danger':''">
    <b-input :value="value" @input="$emit('input', $event)" :placeholder="schema.example"></b-input>
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
  }
}
</script>
