<template>
  <div id="app">
    <button
      v-for="tab in tabs"
      v-bind:key="tab"
      v-bind:class="['tab-button', { active: currentTab === tab }]"
      v-on:click="currentTab = tab"
    >{{ tab }}</button>

    <component v-bind:is="currentTabComponent" class="tab"></component>
  </div>
</template>

<script>
import QrCamera from "@/components/QrCamera";
import QrUpload from "@/components/QrUpload";
import QrDragDrop from "@/components/QrDragDrop";
export default {
  name: "app",
  components: {
    QrCamera,
    QrUpload,
    QrDragDrop
  },
  data() {
    return {
      currentTab: "Camera",
      tabs: ["Camera", "Upload", "DragDrop"]
    };
  },
  computed: {
    currentTabComponent: function() {
      return "Qr" + this.currentTab;
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.tab-button {
  padding: 6px 10px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: #f0f0f0;
  margin-bottom: -1px;
  margin-right: -1px;
}
.tab-button:hover {
  background: #e0e0e0;
}
.tab-button.active {
  background: #e0e0e0;
}
.tab {
  border: 1px solid #ccc;
  padding: 10px;
}
</style>
