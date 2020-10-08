<template>
  <b-field 
    :label="schema.title" 
    :message="error.message?error.message:schema.description" 
    :type="error.message?'is-danger':''" 
    :class="schema.class?schema.class:''">
    <b-input
      ref="phoneInputElement"
      :value="model"
      @blur="blur($event)" @input="$emit('input', $event)" 
      :placeholder="schema.example">
    </b-input>
  </b-field>

</template>

<script>
import intlTelInput from 'intl-tel-input/build/js/intlTelInput'
import intlTelInputUtils from 'intl-tel-input/build/js/utils'
import clone from 'lodash/clone'

export default {
  name: 'PhoneInput',
  props: [
    'schema',
    'value',
    'error'
  ],
  data () {
    return {
      model: clone(this.value),
      telInput: null
    }
  },
  methods: {
    blur (event) {
      this.$emit('input', event.target.value)
    }
  },
  mounted () {
    const inputElement = this.$refs.phoneInputElement.$refs.input

    this.telInput = intlTelInput(inputElement, {
      utilsScript: intlTelInputUtils,
      preferredCountries: ['es'],
      separateDialCode: true
    })

    inputElement.addEventListener('countrychange', () => {
      this.telInput.setNumber(this.telInput.getNumber())
      this.$emit('input', this.telInput.getNumber())
    })

    setInterval(()=>{
      if(inputElement.value.startsWith('+')) { 
        this.telInput.setNumber(this.model)
      }
    }, 1)

  },
  watch: {
    value:function() {
      this.$emit('input', this.telInput.getNumber())
    }
  }
}
</script>

<style lang="scss">
$flagsImagePath: "~intl-tel-input/build/img/";
@import "~intl-tel-input/src/css/intlTelInput.scss";

.iti {
  width: 100%;
}
</style>
