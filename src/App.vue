<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <h1>researcher2program</h1>
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/jvfe/wikidata_topictagger"
        target="_blank"
        text
      >
        <span class="mr-2">Source Code</span>
        <v-icon>mdi-github</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-row class="justify-center pa-4">
        <v-col xs="12" lg="6">
          <v-sheet elevation="5" color="white" rounded>
            <v-col>
              <h1>Wikidata researcher2program</h1>
              <div class="pt-6 pb-6 pr-8 pl-8">
                <p class="text-justify body-text pb-1">
                  This web app helps attributing affiliation to researcher items
                  on
                  <a
                    target="_blank"
                    href="https://www.wikidata.org/wiki/Wikidata:Main_Page"
                    >Wikidata</a
                  >
                  by gathering
                  <a
                    target="_blank"
                    href="https://quickstatements.toolforge.org/#/"
                    >Quickstatements</a
                  >
                  commands to help you in your task!
                </p>
                <p class="text-justify body-text mb-1">
                  Start typing a researcher like "Sidarta Ribeiro" (<a
                    target="_blank"
                    href="https://www.wikidata.org/wiki/Q7508008 "
                    >Q7508008</a
                  >). Feel free to select multiple!
                </p>
              </div>
              <v-combobox
                v-model="researcher"
                :loading="loadingRComplete"
                :search-input.sync="searchResearcher"
                :items="researchers"
                chips
                deletable-chips
                hint="Start typing to search for a researcher"
                hide-no-data
                no-filter
                color="blue-grey lighten-2"
                label="Search for a researcher"
                item-text="id"
                prepend-icon="mdi-magnify"
                solo
                return-object
                multiple
                auto-select-first
                autofocus
              >
                <template v-slot:item="{ item }">
                  <v-list-item-content>
                    <v-list-item-title>{{
                      `${item.label} (${item.id})`
                    }}</v-list-item-title>
                    <v-list-item-subtitle
                      v-text="item.description"
                    ></v-list-item-subtitle>
                  </v-list-item-content>
                </template>
              </v-combobox>
              <p class="text-justify body-text px-8 pb-6 mb-1">
                In the box below you can type a graduate program like the
                "Graduate Interdisciplinary Program in Bioinformatics (USP)" (<a
                  target="_blank"
                  href="https://www.wikidata.org/wiki/Q102292035 "
                  >Q102292035</a
                >).
              </p>
              <v-autocomplete
                v-model="term"
                :loading="loadingPComplete"
                :search-input.sync="search"
                :items="terms"
                chips
                deletable-chips
                hint="Start typing to search for a program"
                hide-no-data
                hide-selected
                no-filter
                color="teal lighten-2"
                label="Search for a program"
                item-text="id"
                prepend-icon="mdi-magnify"
                solo
              >
                <template v-slot:item="{ item }">
                  <v-list-item-content>
                    <v-list-item-title>{{
                      `${item.label} (${item.id})`
                    }}</v-list-item-title>
                    <v-list-item-subtitle
                      v-text="item.description"
                    ></v-list-item-subtitle>
                  </v-list-item-content>
                </template>
              </v-autocomplete>
            </v-col>
          </v-sheet>
          <v-col> <QSBox :program="term" :research="researcher" /> </v-col>
        </v-col>
      </v-row>
    </v-main>

    <v-footer padless color="primary">
      <v-col class="text-center white--text" cols="12">
        Made by
        <a
          href="https://github.com/jvfe"
          target="_blank"
          class="orange--text text--lighten-1"
        >
          <strong>jvfe</strong></a
        >
      </v-col>
    </v-footer>
  </v-app>
</template>

<script>
import QSBox from "./components/QSBox";
import { searchWikibase } from "./lib/API";

export default {
  name: "App",

  components: {
    QSBox
  },

  data() {
    return {
      term: "",
      researcher: [],
      search: "",
      searchResearcher: "",
      terms: [],
      researchers: [],
      loadingRComplete: false,
      loadingPComplete: false
    };
  },

  watch: {
    search(val) {
      if (!val) return;

      this.searchTerm(val);
    },
    searchResearcher(values) {
      if (!values) return;

      this.searchResearch(values);
    }
  },
  methods: {
    searchTerm: function(val) {
      clearTimeout(this._timerId);

      this._timerId = setTimeout(() => {
        this.fetch(val);
      }, 800);
    },
    searchResearch(values) {
      clearTimeout(this._timerId2);

      this._timerId2 = setTimeout(() => {
        this.fetchResearcher(values);
      }, 800);
    },
    fetch: async function(val) {
      this.loadingPComplete = true;
      this.terms = await searchWikibase(val);
      this.loadingPComplete = false;
    },
    fetchResearcher: async function(values) {
      this.loadingRComplete = true;
      this.researchers = await searchWikibase(values);
      this.loadingRComplete = false;
    }
  }
};
</script>
