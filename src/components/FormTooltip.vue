<!-- FormTooltip.vue -->
<template>
  <div v-show="showThis" class="formtooltip__help_text hidden-element">
    <strong>For example...</strong><br />
    {{ FormTooltipText }}
  </div>
  <div class="formTooltip" @click="toggleVisibility" v-show="hideThis">
    {{ text }}
  </div>
</template>


<script>
export default {
  name: "formTooltip",
  props: {
    text: String,
    FormTooltipText: String,
  },
  data() {
    return {
      showThis: false, // Initially hidden
      hideThis: true,
    };
  },
  methods: {
    toggleVisibility() {
      this.showThis = true; // Toggle visibility on click
      this.hideThis = false;
    },
  },
};
</script>

<style scoped>
.formTooltip {
  font-size: 0.75em;
}

.formtooltip__help_text {
  background:rgba(0,0,0,0.05);
  padding:0.5em;
}

</style>
