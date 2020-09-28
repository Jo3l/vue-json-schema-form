<template>
  <b-field :label="schema.title" :message="schema.description">
    <b-datepicker
        append-to-body
        :value="value ? moment(value).toDate() : null"
        @input="value = moment($event).format('Y-MM-DD');schemaPerson.description='('+moment().diff(value, 'years',false)+' años)';$emit('input', $event)"
        placeholder="Selecciona..."
        icon="calendar"
        expanded
        position="is-bottom-left">
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
