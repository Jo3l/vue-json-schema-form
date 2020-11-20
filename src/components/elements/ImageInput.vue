<template>
  <div class="field image-input">
    <label class="label">{{schema.title}}</label>
    <div style="border: 1px solid #dbdbdb;border-radius: 4px;padding: 1em;" :class="error.message?'control has-text-centered has-icons-right':'control has-text-centered'">
    <image-uploader
    class="container"
    :disabled="schema.disabled"
    :debug="1"
    :maxWidth="512"
    :quality="0.6"
    :autoRotate=true
    outputFormat="string"
    :preview=false
    className="fileinput is-hidden"
    :capture="true"
    accept="image/*"
    doNotResize="['gif', 'svg']"
    @input="setImage"
  >
        <label for="fileInput" slot="upload-label">
        <picture v-if="image" :style="'background-image:url('+image+')'" style="width:100%;display: block;padding-top: 100%;background-size: cover; background-position: center;"></picture>
        <small style="font-size: .5em;text-align: right;display: block;">{{image?(byteLength(image) / 1000).toFixed(1) + " kb":""}}</small>
        <figure>
          <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 32 32">
              <path class="path1" d="M9.5 19c0 3.59 2.91 6.5 6.5 6.5s6.5-2.91 6.5-6.5-2.91-6.5-6.5-6.5-6.5 2.91-6.5 6.5zM30 8h-7c-0.5-2-1-4-3-4h-8c-2 0-2.5 2-3 4h-7c-1.1 0-2 0.9-2 2v18c0 1.1 0.9 2 2 2h28c1.1 0 2-0.9 2-2v-18c0-1.1-0.9-2-2-2zM16 27.875c-4.902 0-8.875-3.973-8.875-8.875s3.973-8.875 8.875-8.875c4.902 0 8.875 3.973 8.875 8.875s-3.973 8.875-8.875 8.875zM30 14h-4v-2h4v2z"></path>
          </svg>
        </figure>

        <span class="upload-caption">{{ hasImage ? 'Reemplazar Imagen' : 'Subir Imagen' }}</span>
      </label>
  </image-uploader>
    </div>
    <p :class="error.message?'help is-danger':'help'">{{error.message?error.message:schema.description}}</p>
  </div>
</template>

<script>
import ImageUploader from 'vue-image-upload-resize'

export default {
  name: 'ImageElement',
  components: {
    ImageUploader
  },
  props: [
    'schema',
    'value',
    'error'
  ],
  data () {
  return {
    hasImage:false,
    image:''
  }
  },
  methods: {
    byteLength(str) {
      // returns the byte length of an utf8 string
      var s = str.length;
      for (var i=str.length-1; i>=0; i--) {
        var code = str.charCodeAt(i);
        if (code > 0x7f && code <= 0x7ff) s++;
        else if (code > 0x7ff && code <= 0xffff) s+=2;
        if (code >= 0xDC00 && code <= 0xDFFF) i--; //trail surrogate
      }
      return s;
    },
    setImage: function(output) {
      this.hasImage = true;
      this.image = output;
      this.$emit('input', output)
    }
  }
}
</script>
