<template>
  <v-sheet color="accent lighten-4" elevation="10" rounded class="pa-2">
    <v-col>
      <v-btn @click="copyCommands" color="warning" class="ma-2">
        <v-icon small>mdi-clipboard-outline</v-icon>
        Copy
      </v-btn>
      <v-btn
        color="accent darken-3"
        href="https://quickstatements.toolforge.org/#/batch"
        target="_blank"
        class="ma-2"
      >
        <v-icon small>mdi-arrow-top-right</v-icon>
        Go to Quickstatements
      </v-btn>
      <v-textarea
        outlined
        readonly
        height="100%"
        :loading="loadingQS"
        class="quickstatements mt-1"
        label="Quickstatements"
        :value="quickstatements"
        id="qsArea"
      ></v-textarea>
    </v-col>
  </v-sheet>
</template>

<script>
import { formatQS } from "../lib/API";

export default {
  name: "QSBox",
  props: ["program", "research"],
  data: () => ({
    quickstatements: "",
    loadingQS: false
  }),

  watch: {
    // TODO: Improve this, one watcher with same handler
    program(val) {
      if (val != null) {
        this.createQS();
      } else {
        this.quickstatements = "";
      }
    },
    research(val) {
      if (val != null) {
        this.createQS();
      } else {
        this.quickstatements = "";
      }
    }
  },

  methods: {
    createQS: function() {
      this.loadingQS = true;
      let QSstring = "";
      this.research.forEach(researcher => {
        QSstring += formatQS(researcher.id, this.program) || "";
      });
      this.quickstatements =
        QSstring == ""
          ? "Wasn't able to build the QuickStatements 😥"
          : QSstring;
      this.loadingQS = false;
    },
    copyCommands: function() {
      const qsAreaElement = document.querySelector("#qsArea");
      if (!navigator.clipboard) {
        // Fallback copy
        qsAreaElement.select();
        document.execCommand("copy");
      } else {
        navigator.clipboard.writeText(qsAreaElement.value);
      }
    }
  }
};
</script>

<style scoped>
.quickstatements {
  font-family: monospace;
}
</style>
