<template>
  <div id="box">
    <div id="margin" class="box">
      <span
        class="main-label"
        :class="{'is-context': context == 'ma'}"
        @click="setContext('ma')">margin {{spacing.m.a ? '(' + spacing.m.a + ')' : ''}}</span>
      <div
        class="label t"
        :class="{'is-context': context == 'mt'}"
        @click="setContext('mt')">
        {{spacing.m.t ? spacing.m.t : '-'}}
      </div>
      <div
        class="label b"
        :class="{'is-context': context == 'mb'}"
        @click="setContext('mb')"
      >{{spacing.m.b ? spacing.m.b : '-'}}</div>
      <div
        class="label l"
        :class="{'is-context': context == 'ml'}"
        @click="setContext('ml')"
      >{{spacing.m.l ? spacing.m.l : '-'}}</div>
      <div
        class="label r"
        :class="{'is-context': context == 'mr'}"
        @click="setContext('mr')"
      >{{spacing.m.r ? spacing.m.r : '-'}}</div>
    </div>
    <div id="padding" class="box">
      <span
        class="main-label"
        :class="{'is-context': context == 'pa'}"
        @click="setContext('pa')"
      >padding {{spacing.p.a ? '(' + spacing.p.a + ')' : ''}}</span>
      <div
        class="label t"
        :class="{'is-context': context == 'pt'}"
        @click="setContext('pt')"
      >{{spacing.p.t ? spacing.p.t : '-'}}</div>
      <div
        class="label b"
        :class="{'is-context': context == 'pb'}"
        @click="setContext('pb')"
      >{{spacing.p.b ? spacing.p.b : '-'}}</div>
      <div
        class="label l"
        :class="{'is-context': context == 'pl'}"
        @click="setContext('pl')"
      >{{spacing.p.l ? spacing.p.l : '-'}}</div>
      <div
        class="label r"
        :class="{'is-context': context == 'pr'}"
        @click="setContext('pr')"
      >{{spacing.p.r ? spacing.p.r : '-'}}</div>
    </div>
    <div id="content">
      <div class="output">
        {{output.join(' ')}}
        <span
          v-if="context && output.length"
        >
          <v-btn
            x-small
            outlined
            icon
            color="red"
            @click="clear"
          ><v-icon x-small>mdi-close</v-icon></v-btn>
        </span>
      </div>
      <VSlider
        v-if="context"
        v-model="spacing[context[0]][context[1]]"
        thumb-label
        :min="0"
        :max="16"
      />
    </div>
  </div>
</template>
<script>
  const spacing = ()=>{
    return {
      m:{
        a:null,
        t:null,
        b:null,
        r:null,
        l:null
      },
      p:{
        a:null,
        t:null,
        b:null,
        r:null,
        l:null
      }
    }
  }
  import {VSlider} from 'vuetify/lib'
  export default {
    name: 'MagicSpacer',
    components:{VSlider},
    props:['value'],
    data: () => ({
      context:null,
      spacing:spacing(),
      draft:null,
      flag:true
    }),
    computed:{
      output(){
        let classArray = []
        Object.keys(this.spacing).forEach(type=>{
          let flag = false
          Object.keys(this.spacing[type]).forEach(key=>{
            if (this.spacing[type][key] && !flag){
              if (key == 'a'){
                flag = true
              }
              classArray.push(type + key + '-' + this.spacing[type][key])
            }
          })
        })
        return classArray
      }
    },
    watch:{
      output(val){
        this.$emit('input',val)
        console.log(val)
      }
    },
    mounted(){
      console.log('mounted')
    },
    updated(){
      console.log('updated')
    },
    methods:{
      setContext(e){
        this.context = e;
      },
      clear(){
        this.$set(this,'spacing',spacing())
      },
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
<style scoped>
#box{
  position:relative;
  width:250px;
  height:175px;
}
#margin{
  position:absolute;
  top:0;
  left:0;
  background:#FFCC80;
  width:250px;
  height:175px;
  outline:1px dotted black;
  z-index:1;
}
#padding{
  position:absolute;
  top:25px;
  left:25px;
  background:#C5E1A5;
  width:200px;
  height:125px;
  outline:1px dotted black;
  z-index:2;
}
#content{
  position:absolute;
  top:50px;
  left:50px;
  background:white;
  width:150px;
  height:75px;
  outline:1px dotted black;
  z-index:3;
}
.box .main-label{
  min-width:25px;
  text-align:center;
  position:absolute;
  height:25px;
  padding-left:5px;
  padding-right:5px;
  font-size:10px;
  line-height:25px;
  color:rgba(0,0,0,0.88);
  text-transform: uppercase;
}
.box:hover {
  background:lightblue !important;
}
.box:hover .main-label{
  color:black;
  font-weight:bolder;
  cursor:pointer;
}

.label{
  position:absolute;
  width:25px;
  height:25px;
  padding:0;
  text-align:center;
  font-size:13px;
  line-height:25px;
  color:black;
  cursor:pointer;
}
.t{
  top:0;
  left:calc(50% - 12px);
}
.b{
  bottom:0;
  left:calc(50% - 12px);
}
.l{
  top:calc(50% - 12px);
  left:0;
}
.r{
  top:calc(50% - 12px);
  right:0;
}
.is-context{
  background:yellow;
  font-weight:bolder !important;
}
.output{
  height:25px;
  padding:5px;
}
</style>
