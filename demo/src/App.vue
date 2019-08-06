<template>
  <div class='demo_container'>
    <section class="button_section">
      <button v-on:click="reset_text">Reset Target Text</button>
      <button v-on:click="add_buffalo">Add Buffalo</button>
    </section>

    <div class="dnd_box">
      <dnd-comp
          heading="Places to Travel"
          :items="items"
          :css_variables="css_variables"
          v-on:dnd_comp_cancelled="dnd_cancelled">
      </dnd-comp>
    </div>
    <div class="target_section">
      <div class="label_box">Your Destination:</div>
      <div
          ref="target_box"
          class="target_box"
          v-on:drop="target_drop($event)"
          v-on:dragover="target_dragover($event)">'DND Your Destination Here'</div>
    </div>
  </div>
</template>

<script>
  import DndComp from 'dndcomp';
  export default {
    name: 'App',
    data() {
      return {
        items: [
          'New York',
          'Boston',
          'Cleveland',
          'San Francisco',
          'Chicago',
          'Detroit',
          'St. Louis',
          'Toledo'
        ],
        css_variables: {
          dnd_comp_heading_color: 'white',
          dnd_comp_icon_color: 'white',
          dnd_comp_items_panel_color: 'white',
          dnd_comp_items_panel_background: 'transparent',
          dnd_comp_items_panel_border: '1px solid white'
        }
      }
    },
    components: {
      DndComp
    },
    methods: {
      reset_text: function(){
        this.$refs.target_box.innerText = 'DND Your Destination Here';
      },
      target_drop: function(e){
        const text = e.dataTransfer.getData("text");
        e.target.innerText = text;
        console.log(`Travel Destination: ${text}`);
      },
      target_dragover: function(e){
        e.preventDefault();
        e.dataTransfer.dropEffect = 'copy';
      },
      add_buffalo: function(){
        this.items.push('Buffalo');
      },
      //child to parent
      dnd_cancelled: function(source_text){
        console.log(`Drop cancelled from: ${source_text}`);
      }
    }
  }
</script>

<style>
  .demo_container {
    width: 100%;
    height: 100%;
    max-width: 70rem;
    max-height: 60rem;
    margin-top: 2rem;
    color: white;
    background-color: black;
    padding: 1rem;
    font-size: 1rem;
    font-weight: bold;
  }
  .button_section {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 15rem;
  }
  .dnd_box {
    margin: 3rem 0 0 3rem;
  }
  .target_section {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 21rem;
    margin: 6rem 0 0 20rem;
  }
  .label_box {
    align-self: center;
  }
  .target_box {
    width: 13rem;
    padding: .5rem;
    border: 2px solid white;
  }
  button {
    cursor: pointer;
  }
</style>