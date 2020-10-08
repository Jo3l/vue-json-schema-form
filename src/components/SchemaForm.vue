<template>
  <form @submit.prevent>
    <template v-for="(property, key) in schema.properties">
      <slot :name="key" :item="{key: key, schema: property, value: items[key], update: updateValue, getvalue: getValue}">
        <component :is="element" :key="key" :schema="property" :value="items[key]" :error="ajvErrors[key]?ajvErrors[key]:ajvErrors" @input="updateValue($event, key)">
        </component>
      </slot>
    </template>
    <slot name="actions">
      <div class="control">
        <button type="submit" class="button is-primary"><span>Enviar</span></button>
      </div>
    </slot>
    <pre>
      {{ajvErrors}}
      </pre>
  </form>
</template>

<script>
import Ajv from 'ajv'
import JSONPointer from 'json-pointer'
import FormElement from '@/components/elements/FormElement'
import { scaffoldFromSchema, pruneEmptyMembers } from '@/utility/json-schema-helpers'

let ajv = new Ajv({allErrors: true, verbose:true, uniqueItems:false, jsonPointers: false, format: 'full'})
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
        return true;
      }
      else {
        this.buildErrors()
        return false;
      }
    },
    validate() {
      // validate against schema
      return ajv.validate(this.schema, pruneEmptyMembers(this.items))
    },
    buildErrors(key) {
      let errors = {};

      ajv.errors.every((error) => {
        const schemaKey = error.dataPath.split('.')[error.dataPath.split('.').length-1];
        if (error.keyword === 'required') {
            const path = error.dataPath.length === 0 ? `/properties/${error.params.missingProperty}` : error.schemaPath.substring(1, error.schemaPath.length - 8) + `properties/${error.params.missingProperty}`
            const property = JSONPointer.get(this.schema, path)
            if(!key || key==error.params.missingProperty) errors[error.params.missingProperty] = { missingProperty: error.params.missingProperty, message: 'El campo ' + property.title + ' es necesario'};
            return true;
        } else if (error.keyword === 'format') {
            const path = error.schemaPath.substring(1, error.schemaPath.length - 7)
            const property = JSONPointer.get(this.schema, path)
            if(!key) errors[schemaKey] = { message: property.title +' no tiene un formato correcto Ej: '+ property.example }
            return true;
        } else if (error.keyword === 'pattern') {
            const path = error.schemaPath.substring(1, error.schemaPath.length - 8)
            const property = JSONPointer.get(this.schema, path)
            if(!key) errors[schemaKey] = { message: property.title +' no tiene un formato correcto Ej: '+ property.example }
            return true
        } else if (error.keyword === 'minLength') {
            const path = error.schemaPath.substring(1, error.schemaPath.length - 10)
            const property = JSONPointer.get(this.schema, path)
            if(!key) errors[schemaKey] = { message: property.title +' no puede ser inferior a ' + error.params.limit + ' caracteres.'}
            return true;
        } else if (error.keyword === 'maxLength') {
            const path = error.schemaPath.substring(1, error.schemaPath.length - 10)
            const property = JSONPointer.get(this.schema, path)
            if(!key) errors[schemaKey] = { message: property.title +' no puede ser superior a ' + error.params.limit + ' caracteres.'}
            return true;
        } else {
            errors[schemaKey] = {};
            return true;
        }
      })
      if(errors) {
        //this.ajvErrors = {...this.ajvErrors, ...errors};
        this.ajvErrors = errors;
      }
    },
    getValue(child) {
      return this.items[child];
    },
    updateValue (value, child) {
      this.items[child] = value;
      
      if(!this.validate()){
        if (typeof(this.items[child]) === 'object') {
          Object.keys(this.items[child]).forEach((nested)=> {
            if(nested!==undefined && nested!=='undefined')this.buildErrors(nested);
          });
        } else {
          this.buildErrors(child);
        }
      } else {
        this.ajvErrors={};
      }
    }
  },
  mounted() {

  }
}
</script>
