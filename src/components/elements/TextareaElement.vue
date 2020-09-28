<template>
  <b-field :label="schema.title" :message="schema.description">
    <b-input type="textarea" :minlength="schema.minLength" :maxlength="schema.maxLength" :value="value" @input="$emit('input', $event)" :type="error?'is-danger':''">
    </b-input>
  </b-field>
</template>

<script>
export default {
  name: 'TextareaElement',
  props: [
    'schema',
    'value',
    'error'
  ]
}
</script>
