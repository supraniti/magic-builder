<template>
  <component
    v-if="edit"
    :is="tag"
    :class="tClass"
    :placeholder="placeholder"
    :contenteditable="edit"
    @input="saveDraft"
    @blur="save"
    @click="click"
  />
  <component
    v-else
    :is="tag"
    :class="tClass"
    :contenteditable="edit"
    :placeholder="placeholder"
    v-text="value"
    @click="click"
  />
</template>
<script>
export default {
  name: 'EditableText',
  props:['value','placeholder','tClass','tag'],
  data: () => ({
    draft:null,
    edit:false
  }),
  computed:{
    isDraft(){
      return this.value !== this.draft
    }
  },
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
    },
    save(){
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
