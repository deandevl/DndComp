<template>
  <div class="dndComp" tabindex="0" v-on:blur="blur_it">
    <section class="dndComp_headSec">
      <div :class="show_it ? 'dndComp_arrowIcon__open' : 'dndComp_arrowIcon__closed'" v-on:click="icon_click"></div>
      <span v-show="heading">{{heading}}</span>
    </section>
    <section class="dndComp_itemsSec" v-if="items.length > 0">
      <div class="dndComp_itemsPanel" :style="panel_style">
        <div
            class="dndComp_itemBox"
            draggable="true"
            v-for="(item,index) in items"
            :key="index"
            v-on:dragstart="dragstart_handler($event)"
            v-on:dragend="dragend_handler($event)">
          {{item}}
        </div>
      </div>
    </section>
  </div>
</template>

<script>
  export default {
    name: 'DndComp',
    data(){
      return {
        show_it: false,
        panel_style: 'height: 0; transition: all 3s; opacity: 0;'
      }
    },
    props: {
      items: {
        type: Array,
        default: () => {
          return null;
        }
      },
      heading: {
        type: String,
        default: null
      },
      drop_panel_height: {
        type: String,
        default: '100px'
      },
      css_variables: {
        type: Object,
        default: () => {
          return null;
        }
      }
    },
    methods: {
      blur_it: function(){
        if(this.show_it){
          this.show_it = false;
          this.panel_style = 'height: 0;transition: all 3s; opacity: 0;';
        }
      },
      icon_click: function(){
        this.show_it = !this.show_it;
        this.toggle_panel_state();
      },
      toggle_panel_state: function(){
        if(this.show_it){
          this.panel_style = 'height: ' + this.drop_panel_height + ';transition: all 3s; opacity: 1.0;';
        }else {
          this.panel_style = 'height: 0;transition: all 3s; opacity: 0;';
        }
      },
      dragstart_handler: function(e){
        //const id = e.target.id; //debug
        e.dataTransfer.setData('text/plain', e.target.innerText);
        e.dataTransfer.dropEffect = 'copy';
        e.dataTransfer.effectAllowed = 'copy';
        return true;
      },
      dragend_handler: function(e){
        const status = e.dataTransfer.dropEffect;
        if(status === 'none'){
          this.$emit('dnd_comp_cancelled',e.target.innerText);
        }
      }
    },
    mounted(){
      if(this.css_variables !== null){
        for(let key of Object.keys(this.css_variables)){
          this.$el.style.setProperty(`--${key}`, this.css_variables[key]);
        }
      }
    }
  }
</script>

<style lang="less">
  :root {
    --dnd_comp_font_family: Verdana,serif;

    --dnd_comp_heading_color: black;
    --dnd_comp_heading_font_size: 1rem;
    --dnd_comp_heading_font_weight: bold;

    --dnd_comp_down_icon: '\21D3';
    --dnd_comp_icon_color: black;

    --dnd_comp_items_panel_position: static;
    --dnd_comp_items_panel_z_index: auto;
    --dnd_comp_items_panel_color: black;
    --dnd_comp_items_panel_background: white;
    --dnd_comp_items_panel_border: 1px solid black;

    --dnd_comp_item_font_size: .8rem;
    --dnd_comp_item_hover_box_shadow: 0 0 10px gray;
    --dnd_comp_item_hover_color: violet;
  }

  .dndComp {
    font-family: var(--dnd_comp_font_family);
    &:focus {
      outline: none;
    }

    &_headSec {
      display: flex;
      flex-direction: row;
      color: var(--dnd_comp_heading_color);
      font-size: var(--dnd_comp_heading_font_size);
      font-weight: var(--dnd_comp_heading_font_weight);
    }

    &_arrowIcon__open::before {
      content: var(--dnd_comp_down_icon);
      display: inline-block;
      color: var(--dnd_comp_icon_color);
      transition: 600ms linear all;
      margin-right: .2rem;
      font-size: 1rem;
      cursor: pointer;
    }
    &_arrowIcon__closed::before {
      content: var(--dnd_comp_down_icon);
      display: inline-block;
      color: var(--dnd_comp_icon_color);
      transform: rotate(-90deg);
      transition: 600ms linear all;
      margin-right: .2rem;
      font-size: 1rem;
      cursor: pointer;
    }

    &_itemsSec {
      position: relative;
    }

    &_itemsPanel {
      color: var(--dnd_comp_items_panel_color);
      background: var(--dnd_comp_items_panel_background);
      border: var(--dnd_comp_items_panel_border);
      border-bottom-left-radius: 4px;
      border-bottom-right-radius: 4px;
      width: max-content;
      overflow: auto;
      padding: .5rem;
      position: var(--dnd_comp_items_panel_position);
      z-index: var(--dnd_comp_items_panel_z_index);
      transition: all 2s ease-in-out;

      &::-webkit-scrollbar-track {
        background-color: #F5F5F5;
        border-radius: 10px;
        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
      }
      &::-webkit-scrollbar {
        background-color: transparent;
        width: 12px;
        height: 12px;
      }
      &::-webkit-scrollbar-thumb {
        background-color: #D62929;
        border-radius: 10px;
        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
      }
    }

    &_itemBox {
      cursor: pointer;
      margin: .2rem 0 .2rem .3rem;
      font-size: var(--dnd_comp_item_font_size);

      &:hover {
        color: var(--dnd_comp_item_hover_color);
        box-shadow: var(--dnd_comp_item_hover_box_shadow);
      }
    }
  }
</style>