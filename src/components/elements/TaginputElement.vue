<template>
  <b-field :label="schema.title" :type="error?'is-danger':''">
    <b-taginput
      :value="value"
      icon="account-multiple"
      placeholder="Add item"
      @input="$emit('input', $event)">
    </b-taginput>
  </b-field>
</template>

<script>
export default {
  name: 'TaginputElement',
  props: [
    'schema',
    'value',
    'error'
  ]
}
</script>
