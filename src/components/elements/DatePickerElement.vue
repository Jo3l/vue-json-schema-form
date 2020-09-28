<template>
  <b-field :label="schema.title" :message="schema.description">
    <b-datepicker :value="value" @input="$emit('input', $event)" placeholder="Click to select date" :type="error?'is-danger':''">
    </b-datepicker>
  </b-field>
</template>

<script>
export default {
  name: 'DatePickerElement',
  props: [
    'schema',
    'value',
    'error'
  ]
}
</script>
