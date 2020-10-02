<template>
  <b-field 
  :label="schema.title" 
  :message="error.message?error.message:schema.description" 
  :type="error.message?'is-danger':''">
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
