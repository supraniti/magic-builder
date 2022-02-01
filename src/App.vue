<template>
  <v-app
    :class="{
      'edit':manager.edit,
      'dnd':manager.dnd,
      'extra-space':manager.extraMargins
    }"
  >
    <v-app-bar
      app
      color="black"
      dark
      class="d-print-none"
    >
      <v-btn-toggle
        v-model="mode"
      >
        <v-btn
          color="black"
          dark
        >
          <v-icon>mdi-eye</v-icon>
          <span>Preview</span>
        </v-btn>
        <v-btn
          color="black"
          dark
        >
          <v-icon>mdi-square-edit-outline</v-icon>
          <span>Edit</span>
        </v-btn>
        <v-btn
          color="black"
          dark
        >
          <v-icon>mdi-drag</v-icon>
          <span>Rearrange</span>
        </v-btn>
      </v-btn-toggle>
      <v-switch
        v-if="manager.dnd"
        v-model="manager.extraMargins"
        hide-details
        label="Extra Margins"
      />
      <v-spacer></v-spacer>
      <v-menu
        offset-y
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            dark
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>mdi-folder-sync</v-icon>
            <span class="mr-2">Sync</span>
          </v-btn>
        </template>
        <v-list
          dense
          dark
        >
          <v-list-item
            @click="saveModel"
          >
            <v-list-item-icon
              class="mr-1"
            >
              <v-icon small color="green lighten-2">mdi-content-save</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>Save to local storage</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item
            @click="loadModel"
          >
            <v-list-item-icon
              class="mr-1"
            >
              <v-icon small color="green lighten-2">mdi-reload</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>Load from local storage</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item
            @click="resetModel"
          >
            <v-list-item-icon
              class="mr-1"
            >
              <v-icon small color="green lighten-2">mdi-notification-clear-all</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>Reset</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-menu>
      <v-btn
        dark
        @click="clickImport"
      >
        <v-icon>mdi-import</v-icon>
        <span class="mr-2">IMPORT</span>
      </v-btn>
      <input hidden type="file" ref="upload" accept=".json" @change="importJSON">
      <v-menu
        offset-y
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            dark
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>mdi-export</v-icon>
            <span class="mr-2">Export</span>
          </v-btn>
        </template>
        <v-list
          dense
          dark
        >
          <v-list-item
            @click="exportJSON"
          >
            <v-list-item-icon
              class="mr-1"
            >
              <v-icon small color="green lighten-2">mdi-code-json</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>JSON</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item
            @click="exportTemplate"
          >
            <v-list-item-icon
              class="mr-1"
            >
              <v-icon small color="green lighten-2">mdi-vuejs</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>VUE Template</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item
            @click="print"
          >
            <v-list-item-icon
              class="mr-1"
            >
              <v-icon small color="green lighten-2">mdi-file-pdf</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>Print</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-navigation-drawer
      class="d-print-none"
      app
      dark
      color="black"
      width="300px"
      :value="manager.contextComponent && manager.edit ? true :false"
     >
       <v-menu
        v-if="manager.contextComponent"
         offset-y
       >
        <template v-slot:activator="{ on, attrs }">
           <v-btn
             dark
             black
             block
             x-large
             tile
             class="ma-0 pa-2"
             v-bind="attrs"
             v-on="on"
           >
            <v-icon>mdi-dots-vertical</v-icon>
            {{manager.contextModel.component}} Component
           </v-btn>
         </template>
         <v-list
           dense
           dark
         >
           <v-list-item
            :disabled="manager.contextModel.isStructure"
             @click="duplicate"
           >
             <v-list-item-icon
               class="mr-1"
             >
               <v-icon small color="green">mdi-content-copy</v-icon>
             </v-list-item-icon>
             <v-list-item-content>
               <v-list-item-title>Duplicate</v-list-item-title>
             </v-list-item-content>
           </v-list-item>
           <v-list-item
            :disabled="manager.contextModel.isStructure"
             @click="remove"
           >
             <v-list-item-icon
               class="mr-1"
             >
               <v-icon small color="red">mdi-delete</v-icon>
             </v-list-item-icon>
             <v-list-item-content>
               <v-list-item-title>Delete</v-list-item-title>
             </v-list-item-content>
           </v-list-item>
           <v-list-item
             :href="'https://vuetifyjs.com/api/' + camelToHyphen(manager.contextModel.component)"
             target="_blank"
           >
             <v-list-item-icon
               class="mr-1"
             >
               <v-icon small color="blue">mdi-information-variant</v-icon>
             </v-list-item-icon>
             <v-list-item-content>
               <v-list-item-title>API Info</v-list-item-title>
             </v-list-item-content>
           </v-list-item>
         </v-list>
       </v-menu>
      <v-list  style="display:none;"  v-if="manager.contextComponent">
        <v-list-item class="px-2">
          <v-menu
            offset-y
          >
            <template v-slot:activator="{ on, attrs }">
              <v-list-item-avatar>
              <v-btn
                :disabled="manager.contextModel.isStructure"
                dark
                icon
                v-bind="attrs"
                v-on="on"
              >
                <v-icon>mdi-dots-vertical</v-icon>
              </v-btn>
              </v-list-item-avatar>
            </template>
            <v-list
              dense
              dark
            >
              <v-list-item
                @click="duplicate"
              >
                <v-list-item-icon
                  class="mr-1"
                >
                  <v-icon small color="green">mdi-content-copy</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Duplicate</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item
                @click="remove"
              >
                <v-list-item-icon
                  class="mr-1"
                >
                  <v-icon small color="red">mdi-delete</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Delete</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item
                :href="'https://vuetifyjs.com/api/' + camelToHyphen(manager.contextModel.component)"
                target="_blank"
              >
                <v-list-item-icon
                  class="mr-1"
                >
                  <v-icon small color="blue">mdi-information-variant</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>API Info</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-menu>
          <v-list-item-title>{{manager.contextModel.component}} Component</v-list-item-title>
        </v-list-item>
      </v-list>
      <v-divider></v-divider>
      <template v-if="contextParent">
        <v-list
          subheader
          two-line
          dense
        >
          <v-subheader>Parent Component</v-subheader>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title>{{contextParent.model.component}}</v-list-item-title>
            </v-list-item-content>
            <v-list-item-action
            >
            <v-btn
              icon
              @click="setContext(contextParent,'isContextRoot')"
            >
              <v-icon>mdi-cogs</v-icon>
            </v-btn>
            </v-list-item-action>
          </v-list-item>
        </v-list>
      </template>
      <template
        v-if="manager.contextModel && contextProps"
      >
        <v-list
          subheader
          two-line
          dense
        >
          <v-subheader>Props</v-subheader>
          <template
            v-for="(item,index) in contextProps"
          >
            <v-list-item
              :key="index"
            >
              <v-list-item-icon>
                <v-icon>{{item.icon}}</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>{{camelToHyphen(item.key)}}</v-list-item-title>
                <v-list-item-subtitle
                  v-text="getPropValue(item.key)">
                </v-list-item-subtitle>
              </v-list-item-content>
              <v-list-item-action
              >
                <v-switch
                  v-if="item.component == 'VSwitch'"
                  v-model="manager.contextModel.props[item.key]"
                  class="my-0"
                  color="indigo lighten-2"
                  hide-details
                />
                <v-btn
                  icon
                  v-else
                  @click="manager.contextProp = item;contextDialog = true;"
                >
                  <v-icon>mdi-cogs</v-icon>
                </v-btn>
              </v-list-item-action>
            </v-list-item>
            <v-divider
              :key="'d-' + index"
            ></v-divider>
          </template>
        </v-list>
      </template>
      <template
        v-if="manager.contextModel && contextHelpers"
      >
        <v-list
          subheader
          two-line
          dense
        >
          <v-subheader>Class Helpers</v-subheader>
          <template
            v-for="(item,index) in contextHelpers"
          >
            <v-list-item
              :key="index"
            >
              <v-list-item-icon>
                <v-icon>{{item.icon}}</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>{{camelToHyphen(item.key)}}</v-list-item-title>
                <v-list-item-subtitle
                  v-text="getClassValue(item.key)">
                </v-list-item-subtitle>
              </v-list-item-content>
              <v-list-item-action
              >
                <v-btn
                  icon
                  @click="manager.contextProp = item;contextDialog = true;"
                >
                  <v-icon>mdi-cogs</v-icon>
                </v-btn>
              </v-list-item-action>
            </v-list-item>
            <v-divider
              :key="'d-' + index"
            ></v-divider>
          </template>
        </v-list>
      </template>
      <template v-if="manager.contextComponent && manager.contextComponent.$refs.sub">
        <v-list
          subheader
          dense
        >
          <v-subheader>Sub Components</v-subheader>
          <template
            v-for="(value,key) of manager.contextComponent.$refs.sub"
          >
            <v-list-item
              :key="key"
            >
              <v-list-item-action
                v-if="value.model.isActivable"
              >
                <v-switch
                  v-model="value.model.active"
                  class="my-0"
                  color="indigo lighten-2"
                  hide-details
                ></v-switch>
              </v-list-item-action>
              <v-list-item-content>
                <v-list-item-title>{{value.model.component}}</v-list-item-title>
              </v-list-item-content>
              <v-list-item-action
              >
              <v-btn
                icon
                @click="setContext(value,'isSubContext')"
              >
                <v-icon>mdi-cogs</v-icon>
              </v-btn>
              </v-list-item-action>
            </v-list-item>
            <v-divider
              :key="'d-' + key"
            ></v-divider>
          </template>
        </v-list>
      </template>
    </v-navigation-drawer>
    <v-main>
      <div class="magic-canvas" @click="setTarget({target:$event.target,prop:'isContextRoot'})">
        <dnd-zone
          handle-class="handle"
          :validate="validate"
          :native-scroll="false"
          v-if="manager.dnd"
        >
          <magic-component
            :class="{'magic-context': isContext(model)}"
            :component="model.component"
            :contextModel="manager.contextModel"
            :model="model"
            :dnd="manager.dnd"
            v-bind="model.props"
            @add="bottomSheet = true"
          />
        </dnd-zone>
        <magic-component
          v-else
          :class="{'magic-context': isContext(model)}"
          :component="model.component"
          :contextModel="manager.contextModel"
          :model="model"
          v-bind="model.props"
          @add="bottomSheet = true"
        />
      </div>
    </v-main>
    <v-dialog
      v-model="contextDialog"
      v-if="manager.contextComponent && manager.contextProp"
      max-width="290"
    >
      <v-card>
        <v-card-title class="headline px-5">
          <v-icon class="mr-2">mdi-cogs</v-icon>
          {{manager.contextProp.key}}
        </v-card-title>
        <v-card-text class="pa-5">
          <v-autocomplete
            v-if="manager.contextProp.key == 'iconText'"
            clearable
            cache-items
            :items="icons"
            v-model="manager.contextModel.props[manager.contextProp.key]"
            dense
          >
            <template v-slot:item="data">
              <v-list-item-icon>
                <v-icon>{{'mdi-' + data.item}}</v-icon>
              </v-list-item-icon>
              <v-list-item-content>{{data.item}}</v-list-item-content>
            </template>
          </v-autocomplete>
          <component
            v-else-if="manager.contextProp.type == 'prop'"
            :is="manager.contextProp.component"
            v-bind="manager.contextProp.props"
            v-model="manager.contextModel.props[manager.contextProp.key]"
          />
          <component
            v-else-if="manager.contextProp.type == 'class'"
            :is="manager.contextProp.component"
            v-bind="manager.contextProp.props"
            v-model="manager.contextModel.class[manager.contextProp.key]"
          />
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-bottom-sheet
      v-model="bottomSheet"
      inset
    >
      <v-card>
        <v-toolbar
        >
          <v-icon class="mr-2">mdi-view-grid-plus</v-icon>
          <v-toolbar-title>Inject Components</v-toolbar-title>
        </v-toolbar>
        <v-tabs vertical color="black" v-model="tab">
          <v-tab
            v-for="(group,key) in contextPrototypes"
            :key="key"
            :disabled="group.length < 1"
          >
            {{key}}
          </v-tab>
          <v-tabs-items vertical v-model="tab">
            <v-tab-item
              v-for="(group,key) in contextPrototypes"
              :key="key"
            >
              <v-card flat>
                <v-card-text>
                  <v-chip
                    class="ma-2"
                    large
                    label
                    v-for="item in group"
                    :key="item.text"
                    @click="addToModel(item)"
                  >
                    <v-icon left>
                      {{item.icon}}
                    </v-icon>
                    {{ item.text }}
                  </v-chip>
                </v-card-text>
              </v-card>
            </v-tab-item>
          </v-tabs-items>
        </v-tabs>
      </v-card>
    </v-bottom-sheet>
  </v-app>
</template>

<script>
import MagicComponent from './components/MagicComponent'
import MagicColorPicker from './components/MagicColorPicker'
import MagicIconPicker from './components/MagicIconPicker'
import MagicSpacer from './components/MagicSpacer'
import {VSwitch} from 'vuetify/lib'
import {VSelect} from 'vuetify/lib'
import {VColorPicker} from 'vuetify/lib'
import {VTextField} from 'vuetify/lib'
import {VSlider} from 'vuetify/lib'
import config from '@/assets/config.json'
import icons from '@/assets/icons.json'

const validate = (item, container, zone)=>{
  if ( item && container && zone && container.rules && Array.isArray(container.rules.include)){
    return container.rules.include.indexOf(item.dndModel.slug) > -1
  }else{
    return true
  }
}

const initialModel = {
  "id":"root",
  "isContextRoot":true,
  "isNestable":false,
  "isDraggable":false,
  "isStructure":true,
  "isInjectable":false,
  "component":"VContainer",
  "activeProps":["fluid"],
  "classHelpers":[],
  "props":{},
  "children":[
    {
      "slug":"row",
      "isContextRoot":true,
      "isSubContext":true,
      "isNestable":true,
      "isDraggable":true,
      "isStructure":false,
      "isInjectable":true,
      "component":"VRow",
      "activeProps":["noGutters","dense","align","alignContent","justify"],
      "classHelpers":[],
      "class":{},
      "props":{},
      "children":[],
      "rules":{
        "include":["col"]
      },
      "isHandle":false
    }
  ]
}

export default {
  name: 'App',
  components: {
    MagicComponent, MagicColorPicker, MagicIconPicker, MagicSpacer, VSwitch, VSelect, VColorPicker, VTextField, VSlider
  },
  data: () => ({
    validate:validate,
    contextDialog:false,
    bottomSheet:false,
    manager:{
      edit:true,
      dnd:false,
      extraMargins:true,
      target:null,
      contextModel:null,
      contextComponent:null,
      contextProp:null
    },
    mode:1,
    model:initialModel,
    config:config,
    tab:null,
    icons:icons,
    test:null
  }),
  watch:{
    mode(val){
      if (val == 0){
        this.manager.edit = false
        this.manager.dnd = false
      }
      if (val == 1){
        this.manager.edit = true
        this.manager.dnd = false
      }
      if (val == 2){
        this.manager.edit = false
        this.manager.dnd = true
      }
    }
  },
  computed:{
    contextProps(){
      let props = this.manager.contextModel.activeProps.map(prop=>this.config.setters.prop[prop])
      return props.length > 0 ? props : false
    },
    contextHelpers(){
      let helpers = this.manager.contextModel.classHelpers.map(helper=>this.config.setters.class[helper])
      return helpers.length > 0 ? helpers : false
    },
    contextParent(){
      let vnode = this.manager.contextComponent ? this.manager.contextComponent.$parent : false
      while (vnode){
        if (vnode.isContextRoot){
          return vnode
        }else{
          vnode = vnode.$parent
        }
      }
      return null
    },
    contextPrototypes(){
      let result = {}
      let container = this.manager.contextModel
      if (container && this.config.prototypes){
        for ( const [key,value] of Object.entries( this.config.prototypes ) ) {
          result[key] = value.filter(item=>{
            if (container.rules && Array.isArray(container.rules.include)){
              return container.rules.include.indexOf(item.model.slug) > -1
            }else{
              return true
            }
          })
        }
      }
      return result
    }
  },
  methods:{
    uuidv4() {
      return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
        var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8)
        return v.toString(16);
      })
    },
    camelToHyphen(txt){
      return txt.replace(/\B([A-Z])/g, '-$1').toLowerCase()
    },
    getPropValue(key){
      if (this.manager.contextModel && this.manager.contextModel.props && key in this.manager.contextModel.props){
        return this.manager.contextModel.props[key]
      }else{
        return 'undefined'
      }
    },
    getClassValue(key){
      return this.manager.contextModel.class[key]
    },
    isContext(model){
      return this.manager.contextModel == model
    },
    getContext(el,prop){
      let vnode = this.getVnode(el)
      while (vnode){
        if (vnode[prop]){
          return vnode
        }else{
          vnode = vnode.$parent
        }
      }
      return null
    },
    setContext(vnode,prop){
      this.setTarget({target:vnode.$el,prop:prop})
    },
    applyUUID(model){
      if (model.children){
        model.children.forEach(child=>{
          this.applyUUID(child)
        })
      }
      if (!model.uuid){
        model.uuid = this.uuidv4()
      }
    },
    addToModel(item){
      let model = JSON.parse(JSON.stringify(item.model))
      this.applyUUID(model)
      this.manager.contextModel.children.push(model)
    },
    duplicate(){
      let model = this.manager.contextComponent.model
      let component = this.manager.contextComponent
      while (component && component.$parent){
        if (component.model && component.model.children){
          let index = component.model.children.indexOf(model)
          if (index > -1){
            let clone = Object.assign(JSON.parse(JSON.stringify(model)),{uuid:this.uuidv4()})
            component.model.children.splice(index,0,clone)
            return
          }
        }
        component = component.$parent
      }
    },
    remove(){
      let model = this.manager.contextComponent.model
      let component = this.manager.contextComponent
      while (component && component.$parent){
        if (component.model && component.model.children){
          let index = component.model.children.indexOf(model)
          if (index > -1){
            component.model.children.splice(index,1)
            return
          }
        }
        component = component.$parent
      }
    },
    print(){
      window.print()
    },
    saveModel(){
      window.localStorage.setItem('magic-builder', JSON.stringify(this.model))
    },
    loadModel(){
      let model = window.localStorage.getItem('magic-builder')
      if (model){
        this.$set(this,'model',JSON.parse(model))
      }
    },
    resetModel(){
      this.$set(this,'model',JSON.parse(JSON.stringify(initialModel)))
    },
    composedClass(model){
      if (model.class){
        let classArray = Object.values(model.class)
        if (model.isNestable){
          classArray.push('nestable')
        }
        if (model.isHandle){
          classArray.push('handle')
        }
        if (model.component == 'VCol'){
          classArray.push('col')
          if (model.props.cols){
            classArray.push(`col-${model.props.cols}`)
          }
          if (model.props.offset){
            classArray.push(`offset-${model.props.offset}`)
          }
          if (model.props.order){
            classArray.push(`order-${model.props.order}`)
          }
          if (model.props.alignSelf){
            classArray.push(`alignSelf-${model.props.alignSelf}`)
          }
        }
        if (model.component == 'VRow'){
          classArray.push('row')
          if (model.props.noGutters){
            classArray.push(`no-gutters`)
          }
          if (model.props.dense){
            classArray.push(`row--dense`)
          }
          if (model.props.align){
            classArray.push(`align-${model.props.align}`)
          }
          if (model.props.justify){
            classArray.push(`justify-${model.props.justify}`)
          }
          if (model.props.alignContent){
            classArray.push(`align-content-${model.props.alignContent}`)
          }
        }
        return classArray
      }else{
        return ''
      }
    },
    composeTemplate(model,root){
      // let filteredProps = ["innerText"]
      let filteredClasses = ["v-card__actions","row","col"]
      let el
      if (model.component == 'EditableText'){
        el = document.createElement(model.props.tag)
        el.innerText = model.props.value
      }else{
        el = document.createElement(this.camelToHyphen(model.component))
        if (model.props){
          Object.keys(model.props).forEach(key=>{
            let value = model.props[key]
            if (key == "innerText"){
              el.innerText = value
            }else{
              el.setAttribute(this.camelToHyphen(key),value)
            }
          })
        }
        let composedClass = this.composedClass(model)
        if (Array.isArray(composedClass)){
          composedClass.forEach(cls=>{
            if (filteredClasses.indexOf(cls) < 0)
            el.classList.add(cls)
          })
        }
        if (model.children){
          model.children.forEach(child=>{
            this.composeTemplate(child,el)
          })
        }
      }
      root.append(el)
      return root
    },
    exportTemplate(){
      let result = this.composeTemplate(this.model,document.createElement('template'))
      const a = document.createElement("a")
      let html = '<template>' + result.children[0].innerHTML + '</template>'
      a.href = URL.createObjectURL(new Blob([html], {
        type: "text/plain"
      }))
      a.setAttribute("download", "export.vue")
      document.body.appendChild(a)
      a.click()
      document.body.removeChild(a)
    },
    clickImport(){
      this.$refs.upload.click()
    },
    importJSON(e){
      let file = e.target.files[0]
      if (!file){
        alert ('Invalid user input')
      }else{
        var fr = new FileReader()
        fr.onload = (e)=> {
          var result = JSON.parse(e.target.result)
          this.$set(this, 'model', result)
        }
        fr.readAsText(file)
      }
    },
    exportJSON(){
      const a = document.createElement("a")
      a.href = URL.createObjectURL(new Blob([JSON.stringify(this.model, null, 2)], {
        type: "text/plain"
      }))
      a.setAttribute("download", "export.json")
      document.body.appendChild(a)
      a.click()
      document.body.removeChild(a)
    },
    setTarget(e){
      if (!this.manager.edit){
        return
      }
      if ( this.manager.contextComponent && e.target == this.manager.contextComponent.$el ){
        return
      }
      this.manager.contextComponent = this.getContext(e.target,e.prop)
      if (this.manager.contextComponent){
        this.manager.contextModel = this.manager.contextComponent.model
        this.manager.contextProp = null
      }else{
        this.manager.contextModel = false
      }
      this.manager.target = e.target
    },
    getVnode(el){
      while (el && el.parentElement && el !== this.$el){
        if (el.__vue__){
          return el.__vue__
        }else{
          el = el.parentElement
        }
      }
      return null
    },
    isType(value,type){
      let multiple = Array.isArray(value.type)
      let constructors = multiple ? value.type : value.type ?  [value.type] : [value]
      return constructors.indexOf(type) > -1
    },
    log(e){
      console.log(e)
    },
  }
};
</script>
<style>
.magic-text, .magic-html, .magic-text *, .magic-html *{
  position:relative;
  pointer-events:none;
}
.edit .magic-text, .edit .magic-html, .edit .magic-text *, .edit .magic-html *{
  pointer-events:all;
}
.magic-text[contenteditable]:empty::before {
    content: attr(placeholder);
    color: rgba(0,0,0,0.2);
}
.magic-text[contenteditable]:focus{
  outline: 0px solid transparent;
}
.magic-html[contenteditable]:empty::before {
    content: attr(placeholder);
}
.magic-html[contenteditable]:focus{
  outline: 0px solid transparent;
}
.edit .magic-canvas, .dnd .magic-canvas{
  color:rgba(0,0,255,0.2);
  background-image: linear-gradient(currentColor 1px, transparent 1px),linear-gradient(to right, currentColor 1px, transparent 1px);
  background-size: 25px 25px;
  background-repeat: repeat;
  height:100%;
  position:relative;
}
.edit .magic-canvas *:not(.v-image__image){
  position: inherit;
}
.edit .magic-component{
  overflow: visible;
}
.magic-canvas{
  padding:50px;
}
.edit-only, .context-only{
  display:none;
}
.edit .edit-only{
  display:inline;
}
.edit .magic-context > .context-only{
  display:inline;
}
.edit .magic-context::after{
  content: attr(component);
  font-size:12px !important;
  line-height:12px !important;
  color: black !important;
  /* width: calc(100% + 30px); */
  /* height: calc(100% + 30px); */
  width: 100%;
  height:calc(100% + 15px);
  text-align:left !important;
  text-transform: initial;
  box-shadow: 0 0 0 3000px black !important;
  text-shadow:none;
  position: absolute;
  top: -15px;
  left:0;
  /* left: -15px; */
  z-index: 0;
  pointer-events: none;
  background-color: transparent;
  opacity: 0.5;
}
.magic-context::after{
  transition:all 0.2s !important;
}
.edit .magic-component, .edit .magic-component *{
  pointer-events: all;
  cursor:pointer;
}
.edit .row, .edit .col, .edit .container, .edit .magic-html{
  outline:2px dashed rgba(0,255,0,0.5);
  outline-offset:-1px;
  min-height:50px;
  min-width:100px;
}
.dnd .nestable{
  outline: 2px dotted blue;
  outline-offset: -1px;
  min-height: 50px;
}
.dnd .draggable{
  outline: 2px dotted black;
  outline-offset: -1px;
  position: relative;
}
.dnd .draggable:after {
    /* content: "\F01DB";
    display: inline-block;
    font: normal normal normal 24px/1 "Material Design Icons";
    font-size: inherit;
    text-rendering: auto;
    line-height: inherit;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    top: 0;
    left: 0;
    position: absolute;
    font-size: 32px;
    width: 100%;
    height: 100%;
    background: black;
    color: white;
    opacity: 0.5;
    transition:all 0s; */
}
.dnd .draggable:hover {
}
.dnd.extra-space .nestable{
  padding:15px !important;
}
.dnd.extra-space .draggable{
  margin:15px !important;
}
.edit .magic-html{
  display: block;
}
.edit .nestable{
  min-height: 50px;
}
.edit .magic-text{
  min-height:50px;
  outline:2px dashed rgba(255,0,0,0.5);
  outline-offset:-1px;
  display: block;
}
.edit .magic-html.html-edit{
  background:black;
  color: #f0fff8; /* almost white */
  text-shadow: 0 0 3px #80ffc0, 0 0 10px #00ff66, 0 0 20px #00ff66, 0 0 30px #00ff66;
}
.edit .magic-context.row, .edit .magic-context.col, .edit .magic-context.container{
  outline:2px dashed rgba(255,0,0,0.5);
}
.edit .magic-context.row, .edit .magic-context.col{
  /* margin:10px; */
}
.magic-sheet.v-sheet{
  overflow-y:scroll;
}
.edit .magic-context{
  z-index:1;
  /* transform:scale(0.9); */
}
::-webkit-scrollbar{
  background:#fff;
  height:8px;width:8px
}
::-webkit-scrollbar-thumb{
  border:none;
  -webkit-box-shadow:none;
  box-shadow:none;
  background:#dadce0;
  border-radius:8px;
  min-height:40px
}
:hover::-webkit-scrollbar-thumb{
  background:#bdc1c6
}
.v-expansion-panel-content__wrap{
  padding: 0 10px 16px;
}
.edit .v-data-table__wrapper {
    overflow-x: hidden;
}
.dnd .handle {
    cursor: grab;
    /* position: absolute;
    top: 0;
    left: 0; */
}
.handle:hover:after{
  /* content: "\F01DB";
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background: black;
  opacity: 0.5; */
  /* padding: 10px; */
  /* display: inline-block;
  font: normal normal normal 24px/1 "Material Design Icons"; */
  /* font-size: 32px; */
  /* text-rendering: auto;
  line-height: inherit;
  -webkit-font-smoothing: antialiased;
  cursor: grab;   */
}
/* @media print {
    @page {
      size:297mm 210mm;
    }
} */

</style>
