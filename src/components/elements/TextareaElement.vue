<template>
  <b-field 
  :label="schema.title" 
  :message="error.message?error.message:schema.description" 
  :type="error.message?'is-danger':''" 
  :class="schema.class?schema.class:''">
    <b-input type="textarea" :minlength="schema.minLength" :maxlength="schema.maxLength" :value="value" :disabled="schema.disabled" @input="$emit('input', $event)">
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
