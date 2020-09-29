<template>
  <form @submit.prevent="submit">
    <template v-for="(property, key) in schema.properties">
      <slot :name="key" :item="{key: key, schema: property, value: items[key], update: updateValue}">
        <component :is="element" :key="key" :schema="property" :value="items[key]" :error="ajvErrors[key]?ajvErrors[key].message:''" @input="updateValue($event, key)">
        </component>
      </slot>
    </template>
    <slot name="actions">
      <div class="control">
        <button type="submit" class="button is-primary"><span>Submit</span></button>
      </div>
    </slot>
  </form>
</template>

<script>
import Ajv from 'ajv'
import JSONPointer from 'json-pointer'
import FormElement from '@/components/elements/FormElement'
import { scaffoldFromSchema, pruneEmptyMembers } from '@/utility/json-schema-helpers'

let ajv = new Ajv({allErrors: true, jsonPointers: true, format: 'full'})
// fer un array per a que quan fas blur del item 
export default {
  name: 'SchemaForm',
  components: {
    FormElement
  },
  props: {
    schema: {
      type: Object
    },
    value: {
      type: Object
    },
    element: {
      default() {
        return 'form-element'
      }
    }
  },
  data () {
    return {
      items: (this.value !== undefined) ? this.value : scaffoldFromSchema(this.schema),
      ajvErrors: {}
    }
  },
  watch: {
    schema() {
      this.items = scaffoldFromSchema(this.schema)
      ajv = new Ajv({allErrors: true, jsonPointers: true, format: 'full'})
    }
  },
  methods: {
    submit() {
      if (this.validate()) {
        this.$emit('input', pruneEmptyMembers(this.items))
        this.$emit('submit')
      }
      else {
        this.buildErrors()
      }
    },
    validate() {
      // validate against schema
      return ajv.validate(this.schema, pruneEmptyMembers(this.items))
    },
    buildErrors() {
      let errors = {};
      ajv.errors.forEach((error) => {
        if (error.keyword === 'required') {
          const path = error.dataPath.length === 0 ? `/properties/${error.params.missingProperty}` : error.schemaPath.substring(1, error.schemaPath.length - 8) + `properties/${error.params.missingProperty}`
          const property = JSONPointer.get(this.schema, path)
          console.log('ye',property)
          errors[error.params.missingProperty] = { message: 'El campo ' + property.title + ' es necesario'}
        } else if (error.keyword === 'format') {
          const path = error.schemaPath.substring(1, error.schemaPath.length - 7)
          const property = JSONPointer.get(this.schema, path)
          return `${property.title} is not in the correct format. Eg: ${property.example}`
        } else if (error.keyword === 'pattern') {
          const path = error.schemaPath.substring(1, error.schemaPath.length - 8)
          const property = JSONPointer.get(this.schema, path)
          return `${property.title} is not in the correct format. Eg: ${property.example}`
        } else {
          return { property: '', message: ''}
        }
      })
      this.ajvErrors = errors;
    },
    updateValue (value, child) {
      this.items[child] = value;
      
      if(!this.validate()){
        this.buildErrors();
      } else {
        this.ajvErrors={};
      }
    }
  }
}
</script>
