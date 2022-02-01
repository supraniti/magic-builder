<template>
  <v-autocomplete
    :value="value"
    cache-items
    :items="items"
    @change="change"
    dense
  >
    <template v-slot:item="data">
      <v-list-item-icon>
        <v-icon>{{'mdi-' + data.item}}</v-icon>
      </v-list-item-icon>
      <v-list-item-content>{{data.item}}</v-list-item-content>
    </template>
  </v-autocomplete>
</template>

<script>
  export default {
    name: 'MagicIconPicker',
    props:['value','items'],
    data: () => ({
      text:null,
      draft:null
    }),
    mounted(){
    },
    methods:{
      change(e){
        console.log(e)
        this.$emit('input',e)
      },
      save(e){
        this.draft = e
      },
      update(){
        this.$emit('input',this.draft)
      }
    }
  }
</script>
