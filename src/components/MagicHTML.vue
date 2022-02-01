<template>
  <component
    v-if="edit"
    :is="tag"
    class="html-edit"
    :class="tClass"
    :placeholder="placeholder"
    contenteditable
    @input="saveDraft"
    @blur="save"
    @click="click"
  />
  <component
    v-else
    :is="tag"
    :class="tClass"
    v-html="value"
    @click="click"
  />
</template>
<script>
export default {
  name: 'MagicHTML',
  props:['value','placeholder','tClass','tag'],
  data: () => ({
    draft:null,
    edit:false
  }),
  mounted(){
    this.draft = this.value
  },
  updated(){
    if (this.edit){
      this.$el.textContent = this.value
    }
    this.draft = this.value
  },
  methods:{
    click(){
      this.edit = true
      this.$nextTick(()=>{
        this.$el.focus()
      })
    },
    saveDraft(){
      this.draft = this.$el.textContent
      console.log(this.draft)
    },
    save(){
      console.log('save')
      this.$emit('input',this.draft)
      this.edit = false
      // this.$el.innerHTML = this.value
      // this.$el.blur()
    },
    undo(){
      this.$el.textContent = this.value
      this.draft = this.value
    }
  }
}
</script>
