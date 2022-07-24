<template>
  <section>
    <div class="flex">
      <div class="max-w-xs">
        <label for="wallet" class="block text-sm font-medium text-gray-200"
          >Ticker</label
        >
        <div class="mt-1 relative rounded-md shadow-md">
          <input
            v-model="ticker"
            @keydown.enter="add"
            type="text"
            name="wallet"
            id="wallet"
            class="block w-full pr-10 border-gray-500 text-gray-100 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-t bg-gray-500"
            placeholder="For example BTC"
          />
        </div>
        <div
          class="flex bg-gray-500 border-gray-500 shadow-md p-1 rounded-b shadow-md flex-wrap h-8"
        >
          <span
            v-if="!suggestedTickers.length"
            class=" text-opacity-60 px-2 rounded-md text-sm font-medium text-white"
            >No suggestions
          </span>
          <span
            class="inline-flex items-center px-2 m-1 rounded-md text-xs font-medium bg-gray-300 text-gray-800 cursor-pointer"
            v-for="(ticker, idx) in suggestedTickers"
            :key="idx"
            @click="putSuggested(ticker)"
          >
            {{ ticker }}
          </span>
        </div>
        <div class="text-sm text-red-500 pl-1" :valMessage="valMessage">
          {{ valMessage }}
        </div>
      </div>
    </div>
    <add-button @click="add" type="button" :disabled="disabled" class="my-4" />
  </section>
</template>
<script>
import AddButton from "./AddButton.vue";
import { getTickers } from "@/api";

export default {
  components: {
    AddButton
  },

  props: {
    disabled: {
      type: Boolean,
      required: false,
      default: false
    },
    valMessage: {
      type: String,
      required: false,
      default: ""
    }
  },

  emits: {
    "add-ticker": value => typeof value === "string" && value.length > 0,
    "put-suggested": value => typeof value === "string"
  },

  data() {
    return {
      ticker: "",
      allTickers: []
    };
  },

  computed: {
    suggestedTickers() {
      return this.allTickers
        .filter(ticker => ticker.includes(this.ticker.toUpperCase()))
        .slice(0, 4);
    }
  },

  methods: {
    add() {
      if (this.ticker.length === 0) {
        return;
      }
      this.$emit("add-ticker", this.ticker);
      this.ticker = "";
    },
    putSuggested(ticker) {
      this.$emit("put-suggested");
      this.ticker = ticker;
    }
  },
  mounted() {
    getTickers().then(tickers => (this.allTickers = tickers));
  }
};
</script>
