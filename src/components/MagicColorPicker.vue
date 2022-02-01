<template>
  <div
    @click="update"
  >
    <VColorPicker
      show-swatches
      hide-sliders
      hide-mode-switch
      hide-canvas
      hide-inputs
      @input="save"
      :value="value"
    />
  </div>
</template>

<script>
  import {VColorPicker} from 'vuetify/lib'
  export default {
    name: 'MagicColorPicker',
    components:{VColorPicker},
    props:['value'],
    data: () => ({
      draft:null,
      flag:true
    }),
    methods:{
      save(e){
        if (this.flag){
          this.flag = false
        }else{
          if (e.hex){
            this.draft = e.hex
          }else{
            this.draft = e
          }
        }
      },
      update(){
        this.$emit('input',this.draft)
      }
    }
  }
</script>
