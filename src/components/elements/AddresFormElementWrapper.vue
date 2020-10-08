<template>
  <div :class="'fieldSet '+schema.class?schema.class:'field'" ref="address">
    <legend v-if="schema.title" class="is-size-4 has-text-weight-bold column is-full">{{ schema.title }}</legend>
    <div class="content" v-if="schema.description">{{ schema.description }}</div>

    <b-field class="column is-1" :label="showAutocomplete?'Man':'Auto'">
      <button type="button" class="button is-danger" @click="disableAutocomplete">
         <span class="icon is-small">
            <i class="mdi mdi-keyboard"></i>
         </span>
      </button>
    </b-field>

    <b-field 
      v-show="showAutocomplete"
      label="Dirección completa" 
      :message="error[snk]&&error[snk].message?error[snk].message:schema.properties[snk].description" 
      :type="error[snk]&&error[snk].message?'is-danger':''" 
      class="column is-11">
        <b-input
          id="autocomplete"
          :ref="snk" 
          v-model="search"
          :placeholder="schema.properties[snk].example">
        </b-input>
    </b-field>

    <form-element
      v-show="!showAutocomplete"
      v-for="(child, key) in schema.properties" 
      :schema="child" 
      :error="error[key]?error[key]:{message:''}" 
      :value="internalValue[key]" 
      :key="key" 
      @input="updateValue($event, key)"
    ></form-element>

    <div class="column is-full">
        <div ref="map" style="height: 150px"/>
    </div>
    
  </div>
</template>

<script>
// import FormElement from '@/components/elements/FormElement'
export default {
  name: 'FormElementWrapper',
  components: {
    // load at runtime to avoid circular dependency https://vuejs.org/v2/guide/components-edge-cases.html#Circular-References-Between-Components
    FormElement: () => import('@/components/elements/FormElement')
  },
  props: [
    'schema',
    'value',
    'error'
  ],
  data () {
    return {
      snk:'street_address',
      internalValue: (this.value !== undefined) ? this.value : {},
      map: null,
      marker: null,
      autocomplete: null,
      search:null,
      showAutocomplete:true
    }
  },
  watch: {
    value (val) {
      this.internalValue = val
    },
    valueMap (val) {
      if (val && val.latitude && val.longitude) {
        const latLng = {
          lat: this.value.latitude,
          lng: this.value.longitude
        }

        if (this.marker) {
          this.marker.setVisible(false)
          this.marker.setPosition(latLng)
          this.marker.setVisible(true)
        }

        if (this.map) {
          this.map.setCenter(latLng)
          this.map.setZoom(17)
        }
      }
    }
  },
  methods: {
    disableAutocomplete(){
      this.showAutocomplete = !this.showAutocomplete;
      //this.schema.properties.province.disabled=false;
      //this.schema.properties.city.disabled=false;
      //this.schema.properties.postal_code.disabled=false;
    },
    updateValue (value, child) {
      this.internalValue[child] = value
      this.$emit('input', this.internalValue)
      if(this.internalValue.street_address=='') {
        this.showAutocomplete = true;
        this.search = '';
        document.querySelector('#autocomplete').focus();
      }
    },
    initMap () {
      this.map = new window.google.maps.Map(this.$refs.map, {
        center: new window.google.maps.LatLng(40.463667, -3.74922),
        zoom: 13
      })
    },
    initAutocomplete () {
      this.autocomplete = new window.google.maps.places.Autocomplete(this.$refs.street_address.$refs.input, {
        types: ['address']
      })
      this.autocomplete.bindTo('bounds', this.map)
      this.autocomplete.addListener('place_changed', this.onPlaceChanged)
      this.marker = new window.google.maps.Marker({
        map: this.map,
        anchorPoint: new window.google.maps.Point(0, 0)
      })
    },
    onPlaceChanged() {
      const place = this.autocomplete.getPlace()

      if (!place.geometry) {
        return
      }

      this.marker.setVisible(false)

      if (place.geometry.viewport) {
        this.map.fitBounds(place.geometry.viewport)
      } else {
        this.map.setCenter(place.geometry.location)
        this.map.setZoom(17)
      }

      this.marker.setPosition(place.geometry.location)
      this.marker.setVisible(true)

      for (const component of place.address_components) {
        if (component.types.indexOf('street_number') !== -1) {
          this.internalValue['number'] = component.short_name;
          this.$emit('input', this.internalValue['number']);
        }
        if (component.types.indexOf('route') !== -1) {
          this.internalValue['street_address'] = component.long_name
          this.$emit('input', this.internalValue['street_address']);
        }
        if (component.types.indexOf('locality') !== -1) {
          this.internalValue['city'] = component.long_name
          this.$emit('input', this.internalValue['city']);
          //this.schema.properties.city.disabled=true;
        }
        if (component.types.indexOf('administrative_area_level_2') !== -1) {
          this.internalValue['province'] = component.long_name
          this.$emit('input', this.internalValue['province']);
          //this.schema.properties.province.disabled=true;
        }
        if (component.types.indexOf('postal_code') !== -1) {
          this.internalValue['postal_code'] = component.short_name
          this.$emit('input', this.internalValue['number']);
          //this.schema.properties.postal_code.disabled=true;
        }
      }
      this.showAutocomplete = false;
      this.$emit('input', this.internalValue)
      
    }
  },
  mounted: function(){
    this.updateValue()
    this.initMap()
    this.initAutocomplete()
  }
}
</script>

<style scoped>

</style>