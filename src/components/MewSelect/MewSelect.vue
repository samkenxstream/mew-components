<template>
  <!--
  =====================================================================================
    Mew Select
  =====================================================================================
  -->
  <v-select
    height="62"
    class="mew-select rounded-lg"
    color="primary"
    append-icon="mdi-chevron-down"
    :items="selectItems"
    item-text="name"
    item-value="value"
    :label="label"
    :hide-selected="loading"
    :disabled="disabled"
    :error-messages="errorMessages"
    v-model="selectModel"
    @click="onClick"
    return-object
    :menu-props="{ bottom: true, offsetY: true, maxHeight: '419px' }"
    outlined
  >
    <!--
=====================================================================================
  Mew select: Error Messages 
=====================================================================================
-->
    <template v-slot:message="item">
      <span
        class="mew-label"
      >{{ item.message }}
        <a
          rel="noopener noreferrer"
          v-if="buyMoreStr"
          href="https://ccswap.myetherwallet.com/#/"
          target="_blank"
          class="mew-label"
        >{{ buyMoreStr }}</a></span>
    </template>

    <!--
  =====================================================================================
    Filter for dropdown items
  =====================================================================================
  -->
    <template v-slot:prepend-item>
      <v-text-field
        v-if="hasFilter || isCustom"
        ref="filterTextField"
        height="35"
        class="px-2 mew-select-search d-flex align-center"
        color="disabled"
        :placeholder="filterPlaceholder"
        v-model="search"
        flat
        solo
        dense
        hide-details
        prepend-inner-icon="mdi-magnify"
      />
    </template>
    <template v-slot:selection="{ item }">
      <!--
  =====================================================================================
    Select Token Placeholder
  =====================================================================================
  -->
      <div
        v-if="item.selectLabel"
        class="d-flex align-center flex-row justify-space-between full-width basic--text"
      >
        <span>{{ item.text }}</span>
        <v-skeleton-loader
          class="no-pointer-events"
          v-if="loading"
          type="chip"
        />
        <div
          v-if="!loading && item.imgs"
          class="flex-row d-flex align-center"
        >
          <mew-token-container
            class="label-token-img"
            :loading="loading"
            :img="url"
            v-for="(url, idx) in item.imgs"
            :key="url + idx"
            size="small"
          />
          <div
            class="total-token-placeholder inputBorder d-flex align-center justify-center mew-caption"
          >
            <span class="textSecondary--text">+{{ item.total }}</span>
          </div>
        </div>
      </div>
      <!--
  =====================================================================================
    Selected item
  =====================================================================================
  -->
      <div
        v-if="!item.selectLabel"
        class="d-flex align-center justify-center"
      >
        <mew-token-container
          class="ml-1"
          :loading="loading"
          :img="item.img"
          :name="item.name || item"
          size="small"
        />
        <span
          class="text-capitalize mt-1 ml-2 basic--text"
        >{{ item.name ? item.name : item }}
          <span
            v-if="item.subtext"
            class="searchText--text text-capitalize"
          >- {{ item.subtext }}</span></span>
      </div>
    </template>
    <template v-slot:item="data">
      <!--
  =====================================================================================
    Loading Select Dropdown items
  =====================================================================================
  -->
      <v-skeleton-loader
        class="no-pointer-events mew-select-loading"
        min-width="100%"
        v-if="loading"
        type="list-item-avatar"
      />
      <!--
  =====================================================================================
    Default Select Dropdown items
  =====================================================================================
  -->
      <div
        v-if="!isCustom && !loading"
        class="d-flex align-center justify-center"
      >
        <span
          class="text-capitalize ml-2 mt-1"
        >{{ data.item.name ? data.item.name : data.item }}
          <span
            v-if="data.item.subtext"
            class="textSecondary--text text-capitalize"
          >- {{ data.item.subtext }}</span></span>
      </div>
      <!--
  =====================================================================================
    Custom Select Dropdown items
  =====================================================================================
  -->
      <div
        v-if="isCustom && !loading"
        class="d-flex align-center full-width"
      >
        <!--
  =====================================================================================
      Empty Wallet Link
  =====================================================================================
  -->
        <div
          class="no-pointer-events titlePrimary--text"
          v-if="data.item.hasNoEth"
        >
          {{ data.item.text }}
          <a
            class="all-pointer-events"
            target="_blank"
            :href="data.item.link"
          >{{ data.item.linkText }}</a>
        </div>
        <!--
  =====================================================================================
    Custom Dropdown Item
  =====================================================================================
  -->
        <div
          class="d-flex align-center justify-space-between full-width"
          v-if="data.item.name"
        >
          <div
            v-if="!loading"
            class="d-flex align-center"
          >
            <mew-token-container
              class="mr-1"
              :loading="loading"
              :img="!data.item.img ? null : data.item.img"
              :name="data.item.name"
              size="small"
            />
            <span
              class="text-capitalize ml-2 my-2 d-flex flex-column"
            >{{ data.item.symbol || data.item.name || data.item }}
              <span
                v-if="data.item.tokenBalance || data.item.subtext"
                class="mew-caption font-weight-regular textSecondary--text text-capitalize"
              >{{
                data.item.tokenBalance
                  ? data.item.tokenBalance + " " + data.item.symbol
                  : data.item.subtext
              }}</span></span>
          </div>
          <div class="d-flex justify-center flex-column align-end">
            <span>{{ data.item.totalBalance || data.item.price }}</span>
            <span
              class="mew-caption font-weight-regular textSecondary--text"
              v-if="data.item.totalBalance"
            >@ {{ data.item.price }}</span>
          </div>
        </div>
      </div>
    </template>
  </v-select>
</template>
<script>
import MewTokenContainer from '@/components/MewTokenContainer/MewTokenContainer.vue';
import get from 'lodash/get';

export default {
  name: 'MewSelect',
  props: {
    /**
     * Adds a "Buy more" string to the end of the first index of the errorMessages prop.
     */
    buyMoreStr: {
      type: String,
      default: '',
    },
    /**
     * Error messages to display
     */
    errorMessages: {
      type: [String, Array],
      default: '',
    },
    /**
     * Adds filter to select items
     */
    hasFilter: {
      type: Boolean,
      default: true, //  change to false
    },
    /**
     * Filter placeholder
     */
    filterPlaceholder: {
      type: String,
      default: 'Search token name',
    },
    /**
     * MEW select value
     */
    value: {
      type: Object,
      default: () => {
        return {};
      },
    },
    /**
     * Disables the select dropdown.
     */
    disabled: {
      type: Boolean,
      default: false,
    },
    /**
     * Can be an array of objects or array of strings. When using objects, will look for a text and value field.
     * Can also add an img attribute to the object to append an img to the value.
     * Also takes in header objs and a select token label obj, i.e. {text: 'Select Token', imgs: [], total: '', selectLabel: true, divider: true}
     * Please check customItems for more info
     * Example: { name: "Eth", subtext: "Ethereum", value: "Ethereum", img: ethIcon }
     */
    items: {
      type: Array,
      default: () => {
        return [];
      },
    },
    /**
     * Sets the select label
     */
    label: {
      type: String,
      default: '',
    },
    /**
     * Applies Custom Select styles
     */
    isCustom: {
      type: Boolean,
      default: false,
    },
    /**
     * Loading state
     */
    loading: {
      type: Boolean,
      default: false,
    },
  },
  components: {
    MewTokenContainer
  },
  data() {
    return {
      imgError: false,
      selectModel: null,
      selectItems: [],
      search: '',
    };
  },
  watch: {
    search(newVal) {
      if (newVal === '' || newVal === null) {
        this.selectItems = this.items;
      } else {
        const foundItems = this.items.filter((item) => {
          const searchValue = String(newVal).toLowerCase();
          const value = String(get(item, 'value', '')).toLowerCase();
          const name = String(get(item, 'name', '')).toLowerCase();
          const subtext = String(get(item, 'subtext', '')).toLowerCase();
          return (
            name.includes(searchValue) ||
            subtext.includes(searchValue) ||
            value.includes(searchValue)
          );
        });
        this.selectItems = foundItems;
      }
    },
    selectModel(newVal) {
      setTimeout(() => {
        this.search = '';
      }, 1000);
      this.$emit('input', newVal);
    },
    value(newVal) {
      this.selectModel =
        newVal && Object.keys(newVal).length !== 0 ? newVal : this.defaultItem;
    },
    loading() {
      if (!this.loading) {
        this.togglePointerEventStyle();
        this.selectModel =
          this.value && Object.keys(this.value).length !== 0
            ? this.value
            : this.defaultItem;
      }
    },
    items: {
      handler: function(newVal) {
        this.selectItems = newVal;
        this.selectModel =
          this.value && Object.keys(this.value).length !== 0
            ? this.value
            : this.defaultItem;
      },
      deep: true,
    },
  },
  computed: {
    defaultItem() {
      return this.items.find((obj) => {
        return obj.selectLabel || obj.name;
      });
    },
  },
  mounted() {
    this.selectItems = this.items;
    this.selectModel =
      this.value && Object.keys(this.value).length !== 0
        ? this.value
        : this.defaultItem;
  },
  methods: {
    clear(val) {
      this.selectModel =
        val && Object.keys(val).length !== 0 ? val : this.defaultItem;
    },
    togglePointerEventStyle() {
      const elems = document.querySelectorAll('div.v-list-item--link');
      if (elems) {
        const pointerEventStyle = this.loading ? 'none' : 'all';
        for (let i = 0; i < elems.length; i++) {
          elems[i].style.pointerEvents = pointerEventStyle;
        }
      }
    },
    onClick() {
      if (!this.hasFilter && !this.isCustom) {
        return;
      }
      setTimeout(() => {
        this.togglePointerEventStyle();
        if (this.$refs.filterTextField) {
          this.$refs.filterTextField.$refs.input.focus();
        }
      }, 100);
    },
  },
};
</script>
<style lang="scss">
/**
  * Mew Select styles
  */
.mew-select {
  .mdi-chevron-down {
    color: var(--v-titlePrimary-base);
    cursor: pointer;
    font-size: 20px;
  }
  .label-token-img {
    margin-right: -13px;
  }
  .total-token-placeholder {
    border-radius: 50%;
    font-size: 8px;
    height: 24px;
    width: 24px;
  }
  &.v-text-field--enclosed .v-input__append-inner {
    height: 100%;
    margin-top: 0;

    .v-input__icon {
      height: 100%;
    }
  }
  /**
  * Readonly input is not being used (since we are using our own ui via slots) and is taking up unnecessary space
  * so will hide for now
  */
  .v-select__selections {
    input {
      display: none;
    }
  }
}
/**
  * Mew Select Search
  */
.mew-select-search {
  background: var(--v-white-base);
  border-bottom: 1px solid var(--v-dropdownBorder-base);
  border-radius: 5px;
  height: 42px;
  padding: 12px 16px;
  position: sticky;
  top: 0;
  width: 100%;
  z-index: 9999;
  .v-icon {
    font-size: 18px;
  }
}
/**
  * Mew Select Loading
  */
.mew-select-loading {
  .v-skeleton-loader__list-item-avatar {
    .v-skeleton-loader__avatar {
      height: 22px;
      width: 22px;
    }
    .v-skeleton-loader__text {
      height: 14px;
    }
  }
}
</style>
