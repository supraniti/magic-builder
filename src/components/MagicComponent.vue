<template>
  <dnd-container
    v-if="model.isNestable"
    :dnd-model="model.children"
    :dnd-id="model.uuid"
    :rules="model.rules"
    :class="composedClass(model)"
    v-bind="model.props"
  >
    <template
      v-for="item in model.children"
    >
      <dnd-item
        :key="item.uuid"
        :dnd-model="item"
        :dnd-id="model.uuid"
      >
        <magic-component
          :ref="item.isSubContext ? 'sub' : undefined"
          :model="item"
          :class="{'magic-context': isContext(item),draggable:true}"
          :component="item.component"
          :contextModel="contextModel"
          :dnd="dnd"
          v-on="$listeners"
          v-show="item.isActivable ? item.active : true"
        >
          <v-btn
            v-if="item.isInjectable"
            large
            icon
            class="context-only ma-2"
            key="add-button"
            @click="$emit('add')"
          >
            <v-icon>mdi-view-grid-plus</v-icon>
          </v-btn>
        </magic-component>
      </dnd-item>
    </template>
    <v-chip
      v-if="dnd && model.isDraggable"
      key="handle"
      class="ma-2"
      outlined
      label
    >
      <v-icon
        class="handle"
        left
      >
        mdi-drag
      </v-icon>
      {{model.component}}
    </v-chip>
    <v-btn
      v-if="model.isInjectable"
      large
      icon
      key="add-button"
      class="context-only ma-2"
      @click="$emit('add')"
    >
      <v-icon>mdi-view-grid-plus</v-icon>
    </v-btn>
  </dnd-container>
  <component
    v-else-if="model.children"
    :is="model.component"
    class="magic-component"
    :class="composedClass(model)"
    v-bind="model.props"
  >
    <magic-component
      v-for="item in model.children"
      :key="item.uuid"
      :ref="item.isSubContext ? 'sub' : undefined"
      :model="item"
      :dnd="dnd"
      :class="{'magic-context': isContext(item)}"
      :component="item.component"
      :contextModel="contextModel"
      v-on="$listeners"
      v-show="item.isActivable ? item.active : true"
    />
    <v-chip
      v-if="dnd && model.isDraggable"
      key="handle"
      class="ma-2"
      outlined
      label
    >
      <v-icon
        class="handle"
        left
      >
        mdi-drag
      </v-icon>
      {{model.component}}
    </v-chip>
    <v-btn
      v-else-if="model.isInjectable"
      large
      icon
      key="add-component"
      class="context-only ma-2"
      @click="$emit('add')"
    >
      <v-icon>mdi-view-grid-plus</v-icon>
    </v-btn>
  </component>
  <EditableText
    v-else-if="model.component == 'EditableText'"
    class="magic-component magic-text"
    :tag="model.props.tag"
    :t-class="composedClass(model)"
    :placeholder="model.props.placeholder"
    v-model="model.props.value"
  />
  <MagicHTML
    v-else-if="model.rawHTML"
    class="magic-component magic-html"
    :tag="model.props.tag"
    :t-class="composedClass(model)"
    :placeholder="model.props.placeholder"
    v-model="model.props.value"
  />
  <component
    v-else-if="model.props.hasOwnProperty('innerText') && model.iconProp"
    :is="model.component"
    class="magic-component"
    :class="composedClass(model)"
    v-bind="model.props"
  >
    <v-icon left v-if="model.props.iconText && model.props.iconFirst">{{'mdi-' + model.props.iconText}}</v-icon>
    <span v-text="model.props.innerText"></span>
    <v-icon right v-if="model.props.iconText && !model.props.iconFirst">{{'mdi-' + model.props.iconText}}</v-icon>
  </component>
  <component
    v-else-if="model.props.hasOwnProperty('innerText')"
    :is="model.component"
    class="magic-component"
    :class="composedClass(model)"
    v-text="model.props.innerText"
    v-bind="model.props"
  >
  </component>

  <component
    v-else
    :is="model.component"
    class="magic-component"
    :class="composedClass(model)"
    v-bind="model.props"
  >
  </component>
</template>
<script>
import {VCard} from 'vuetify/lib';
import {VCardTitle} from 'vuetify/lib';
import {VCardSubtitle} from 'vuetify/lib';
import {VCardText} from 'vuetify/lib';
import {VCardActions} from 'vuetify/lib';
import {VContainer} from 'vuetify/lib';
import {VRow} from 'vuetify/lib';
import {VCol} from 'vuetify/lib';
import {VImg} from 'vuetify/lib';
import {VSpacer} from 'vuetify/lib';
import EditableText from '@/components/EditableText'
import MagicHTML from '@/components/MagicHTML'

export default {
  name: 'MagicComponent',
  props:['model','contextModel','dnd'],
  computed:{
    isContextRoot(){
      return this.model.isContextRoot
    },
    isSubContext(){
      return this.model.isSubContext
    }
  },
  methods:{
    composedClass(model){
      if (model.class){
        let classArray = Object.values(model.class)
        if (model.isNestable){
          classArray.push('nestable')
        }
        if (model.isHandle){
          classArray.push('handle')
        }
        if (model.component == 'VCardActions'){
          classArray.push('v-card__actions')
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
    isContext(model){
      return this.contextModel == model
    }
  },
  components: {EditableText,MagicHTML,VCard,VCardTitle,VCardSubtitle,VCardText,VCardActions,VContainer,VRow,VCol,VImg,VSpacer}
}
</script>
