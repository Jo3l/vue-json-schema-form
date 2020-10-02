<template>
  <div>
    <section class="hero is-primary">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            Form Demo
          </h1>
          <h2 class="subtitle">
            Form will be automatically rebuilt when the schema changes
          </h2>
        </div>
      </div>
    </section>
    <section class="section">
      <div class="container">
        <div class="columns">
          <div class="column">
            <b-field
              :type="schemaIsValid ? '' : 'is-danger'"
              :message="schemaIsValid ? 'Valid Schema' : 'Invalid Schema'">
              <b-input v-autosize type="textarea" v-model="plainSchema"></b-input>
            </b-field>
          </div>
          <div class="column">
            <schema-form :schema="schema" v-model="obj" @submit="handleSubmit()" />
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import Ajv from 'ajv'
import debounce from 'lodash.debounce'
import SchemaForm from '@/components/SchemaForm'
import draft07 from '@/schema/schema.json'

const ajv = new Ajv()

export default {
  name: 'Demo',
  components: {
    SchemaForm
  },
  computed: {
    plainSchema: {
      get() {
        return JSON.stringify(this.schema, null, 2)
      },
      set: debounce(function(input) {
        try {
          const schema = JSON.parse(input)
          this.schemaIsValid = ajv.validate(draft07, schema)
          this.schema = schema
        } catch (e) {
          this.schemaIsValid = false
        }
      }, 500)
    }
  },
  data () {
    return {
      schema: {
        $id: 'https://example.com/schemaPerson.json',
        $schema: 'http://json-schema.org/draft-07/schema#',
        title: 'Schema',
        type: 'object',
        properties: {
          firstName: {
            type: 'string',
            title: 'Nombre',
            class: '',
            pattern: '^[679]{1}[0-9]{8}$',
            description: 'Nombre del candidato/a.',
            example: 'Nombre'
          },
          lastName: {
            type: 'string',
            title: 'Apellidos',
            class: '',
            description: 'Apellidos del candidato/a.',
            example: 'Apellidos'
          },
          age: {
            title: 'Fecha de nacimiento',
            age: '',
            description: '',
            class: '',
            type: 'string',
            format: 'date'
          },
          contact: {
            type: 'object',
            title: 'Datos de contacto',
            class: 'columns is-multiline',
            properties: {
              phone: { title: 'Teléfono', type: 'string', class: 'column is-half', pattern: "^[679]{1}[0-9]{8}$", minLength: 9, maxLength: 9},
              emeil: { title: 'Email', type: 'string', class: 'column is-half', format:"email"},
            },
            required: ['phone', 'emeil']

          },
          address: {
            type: 'object',
            title: 'Datos de domicilio',
            class: 'columns is-multiline',
            properties: {
              street_address: { title: 'Nombre de la vía', type: 'string', class: 'column is-three-quarters'},
              number: { title: 'Número', type: 'string', class: 'column is-one-quarter' },
              floor: { title: 'Piso', type: 'string', class: 'column is-one-quarter' },
              door: { title: 'Puerta', type: 'string', class: 'column is-one-quarter' },
              stair: { title: 'Escalera', type: 'string', class: 'column is-one-quarter' },
              block: { title: 'Bloque', type: 'string', class: 'column is-one-quarter' },
              postal_code: { title: 'Código Postal', type: 'number', class: 'column is-one-quarter' },
              city: { title: 'Localidad', type: 'string', class: 'column is-half' },
              province: { title: 'Provincia', type: 'string', class: 'column is-one-quarter' }
            },
            required: ['street_address', 'number', 'postal_code', 'city', 'province']
          },
          nickNames: {
            type: "array",
            title: "Nicknames",
            items: {
              type: "string",
              example: "j-dough"
            }
          },
        },
        required: [
          'firstName', 'lastName', 'age'
        ]
      },
      schemaIsValid: true,
      obj: {}
    }
  },
  methods: {
    handleSubmit() {
      
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
