<template>
  <b-field :label="schema.title" :message="schema.description" :class="schema.class?schema.class:''">
    <b-datepicker 
    :value="value" 
    @input="$emit('input', $event)" 
    append-to-body
    placeholder="Selecciona..."
    icon="calendar"
    expanded
    position="is-bottom-left"
    >
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
