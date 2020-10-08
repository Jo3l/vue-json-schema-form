<template>
    <b-field 
    :label="schema.title" 
    :message="error.message?error.message:schema.description" 
    :type="error.message?'is-danger':''" 
    :class="schema.class?schema.class:''">
    <Money
        class="input"
        v-model.lazy="model"
        @blur="blur($event)"
        @input="$emit('input', $event)"
        :precision="options.precision"
        :decimal="options.decimal"
        :thousands="options.thousands"
        :prefix="options.prefix"
        :suffix="options.suffix"
        :masked="options.masked"/>
    </b-field>
</template>

<script>
import { Money } from 'v-money'

export default {
  name: 'MoneyInput',
  components: {
    Money
  },
  props: [
    'schema',
    'value',
    'error'
  ],
  data () {
    return {
      model: this.value ? this.value : '',
      options: {
        precision: 2,
        decimal: ',',
        thousands: '.',
        prefix: '',
        suffix: ' €',
        masked: false
      }
    }
  },
  methods: {
    blur (event) {
      this.$emit('input', event.target.value)
    }
  },
  watch: {
    value (val) {
      if (this.model !== val) {
        this.model = val
      }
    },
    model (val) {
      this.$emit('input', val)
    }
  }
}
</script>
