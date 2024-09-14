<template>
  <div class="container">
    <div class="row col-sm-12">
      <h1>CREATE Prompt Builder for ChatGPT</h1>
      <h5>CREATE is a framework created by Dave Birss</h5>
      <p>
        Imagine ChatGPT is an actor. You need to let it know its role and
        motivation, to really narrow down the response you get.
      </p>
      <p>
        To use this tool, fill in as much as you can into the text fields below,
        click "Generate Prompt", then copy this into your ChatGPT.
      </p>
      <div id="app">
        <!-- Your Vue component template goes here -->
        <template v-for="item in items" :key="item.id">
          <div :class="'row row__' + item.name.toLowerCase() + ' border-top'">
            <div class="col-md-12 position-relative">
              <div class="single-letter">{{ item.letter }}</div>
              <div class="everything-else">
                <h2>{{ item.name }}</h2>
                <p v-if="item.required !== true"><em>(optional)</em></p>
                <p>
                  {{ item.description }}
                  <FormTooltip text="Click to reveal help" :FormTooltipText="item.example">
                  </FormTooltip>
                </p>

                <textarea ref="textarea" v-model="item.input" @input="resizeTextarea($event)" :placeholder="'Type here to build your ' + item.name.toLowerCase()
                  "></textarea>

                <div v-if="item.premade">
                  <p>Or select a premade option below.</p>
                  <div v-for="(value, key) in item.premade" :key="key">
                    <label>
                      <input type="radio" v-model="item.input" :value="value" />
                      {{ key.replace(/_/g, " ") }}
                    </label>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </template>
      </div>
      <div class="col text-center">
        <button id="generatePrompt" class="btn btn-primary" @click="generatePrompt">
          Generate Prompt
        </button>
        <button id="clearPrompt" class="btn btn-link" @click="clearPrompt">
          Clear Prompt &amp; Restart
        </button>
      </div>
    </div>
  </div>
  <div class="generatedPrompt" v-show="showThis">
    <div class="close" @click="closePopup">×</div>
    <div class="generatedPrompt__box">
      <h3>Result:</h3>
      <div class="generatedPrompt__text">
        <p style="white-space: pre-wrap" v-if="generatePrompt">
          {{ generatedPrompt }}
        </p>
      </div>
      <button id="copyPrompt" class="btn btn-primary" @click="copyPrompt">
        Copy Prompt
      </button>
      <div :class="['copyResult', copyResultClass]" v-show="showCopyResult">{{ copyResult }}</div>
    </div>
  </div>
</template>

<script>
import FormTooltip from "./FormTooltip.vue";

export default {
  name: "CreateStructure",
  components: {
    FormTooltip,
  },
  data() {
    return {
      generatedPrompt: "",
      showThis: false, // Initially hidden
      showCopyResult: false,
      copyResult: "",
      items: [],
      selectedValues: {},
    };
  },
  mounted() {
    this.loadData();
  },
  methods: {
    async loadData() {
      try {
        const response = await fetch("/data.json");
        const data = await response.json();
        this.items = data.map((item) => ({ ...item, input: "" }));
      } catch (error) {
        console.error("Error loading data:", error);
      }
    },
    generatePrompt() {
      this.generatedPrompt = this.items
        .filter(item => item.input && item.input.trim() !== "")
        .map((item) => item.input)
        .join("\r\n\r\n");
      this.showThis = true; // Toggle visibility
    },
    closePopup() {
      this.showThis = false; // Toggle visibility
      this.showCopyResult = false;
      this.copyResult = "";
      this.showCopyResult = "";
    },
    clearPrompt() {
      location.reload();
    },
    async copyPrompt() {
      try {
        await navigator.clipboard.writeText(this.generatedPrompt);
        this.copyResult = "Prompt copied to clipboard!";
        this.copyResultClass = "text-success";
        this.showCopyResult = true;
      } catch (err) {
        this.copyResult = "Failed to copy: ", err;
        this.copyResultClass = "text-danger";
        this.showCopyResult = true;
      }
    },
    resizeTextarea(event) {
      const textarea = event.target;
      textarea.style.height = "auto";
      textarea.style.height = textarea.scrollHeight + "px";
    },
  },
  watch: {
    items: {
      deep: true,
      handler(newVal) {
        newVal.forEach((item, index) => {
          // Resize the textarea when the input is updated (e.g. via radio buttons)
          this.$nextTick(() => {
            const textarea = this.$refs.textarea[index];
            if (textarea) {
              textarea.style.height = "auto"; // Reset height
              textarea.style.height = textarea.scrollHeight + "px"; // Adjust height to content
            }
          });
        });
      },
    },
  },
};
</script>

<!-- Your component styles go here -->
<style></style>
