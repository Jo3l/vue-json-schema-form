<template>
  <b-field 
  :label="schema.title" 
  :message="error.message?error.message:schema.description" 
  :type="error.message?'is-danger':''" 
  :class="schema.class?schema.class:''">
    <b-input :disabled="schema.disabled" type="number" :min="schema.minimum" :max="schema.maximum" :step="schema.type === 'number' ? 'any': '1'" :value="value" @input="$emit('input', Number($event))">
    </b-input>
  </b-field>
</template>

<script>
export default {
  name: 'NumberElement',
  props: [
    'schema',
    'value',
    'error'
  ]
}
</script>
