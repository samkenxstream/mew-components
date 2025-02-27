<template>
  <!--
  =====================================================================================
    Mew Icon Button
  =====================================================================================
  -->
  <v-btn
    :target="btnLink ? '_blank' : ''"
    :href="btnLink"
    :class="[buttonClasses, 'mew-icon-button']"
    :color="buttonColor"
    :disabled="disabled"
    depressed
    :outlined="hasOutline"
    :text="isTransparent"
    :width="size"
    :height="size"
    :min-width="size"
    :min-height="size"
    :dark="!disabled"
    :fab="rounded"
  >
    <v-icon
      v-if="mdiIcon && !loading"
      :x-small="mdiIconSize === mdiIconSizes.xsmall"
      :small="mdiIconSize === mdiIconSizes.small"
      :dense="mdiIconSize === mdiIconSizes.dense"
      :large="mdiIconSize === mdiIconSizes.large"
      :x-large="mdiIconSize === mdiIconSizes.xlarge"
    >
      mdi-{{ mdiIcon }}
    </v-icon>

    <!-- TODO: decide if we still want to allow this option -->
    <!-- <img
      v-if="imgIcon && !loading"
      :src="require(`@/assets/images${imgIcon}`)"
      alt="Icon"
      :width="imgIconSize"
      :height="imgIconSize"
    > -->

    <!--
    =====================================================================================
      Loading state
    =====================================================================================
    -->
    <v-progress-circular
      v-if="loading"
      indeterminate
      size="15"
      :color="loadingColor"
    />

    <!--
    =====================================================================================
      Loaded Button Content 
    =====================================================================================
    -->
    <div
      v-if="!loading"
      class="d-flex justify-center align-center"
    >
      <!--
      =====================================================================================
      Button text (if no title prop is passed, then slot should be used)
      TODO: decide if we still want this option.
      =====================================================================================
      -->
      <!-- <span
        v-if="title"
        class="font-weight-regular"
      >{{ title }} </span> -->

      <!--
      =====================================================================================
        slot: default
      =====================================================================================
      -->
      <slot />
    </div>
  </v-btn>
</template>

<script>
export default {
  name: 'MewIconButton',
  props: {
    /**
     * Enables loading state.
     */
    loading: {
      type: Boolean,
      default: false,
    },
    /**
     * Rounds button corners.
     */
    rounded: {
      type: Boolean,
      default: false,
    },
    /**
     * Button sizes: small, medium.
     */
    btnSize: {
      type: String,
      default: 'medium',
    },
    /**
     * Mdi icon size: xsmall, small, medium, large, xlarge.
     */
    mdiIconSize: {
      type: String,
      default: '',
    },
    /**
     * TODO: decide if we still want to allow this option.
     * Image icon size: small, medium.
     */
    // imgIconSize: {
    //   type: String,
    //   default: '20',
    // },
    /**
     * TODO: decide if we still want to allow this option.
     * Sets the button to have 100% width.
     */
    // hasFullWidth: {
    //   type: Boolean,
    //   default: true,
    // },
    /**
     * Applies the button style: background, light, transparent, or outline.
     * If nothing is passed then the button will be the
     * default standard background color theme.
     */
    btnStyle: {
      type: String,
      default: 'background',
    },
    /**
     * TODO: decide if we still want to allow this option.
     * The text that will display at the center of the button.
     * If not passed, a slot should be used.
     */
    // title: {
    //   type: String,
    //   default: '',
    // },
    /**
     * Applies the button color theme: primary, secondary, basic, error.
     */
    colorTheme: {
      type: String,
      default: 'primary',
    },
    /**
     * Removes the ability to click or target the component.
     */
    disabled: {
      type: Boolean,
      default: false,
    },
    /**
     * Designates that the component is a link.
     * when clicked, the link will open up a new page.
     */
    btnLink: {
      type: String,
      default: '',
    },
    /**
     * mdi icon name.
     */
    mdiIcon: {
      type: String,
      default: '',
    },
    /**
     * TODO: decide if we still want to allow this option.
     * img icon path. (base dir: @/assets/images)
     * use) img-icon="/icons/icon-chrome.svg"
     */
    // imgIcon: {
    //   type: String,
    //   default: '',
    // },
  },
  data() {
    return {
      /**
       * all color theme options
       */
      colorThemes: {
        secondary: 'secondary',
        primary: 'primary',
        basic: 'basic',
        error: 'error',
      },
      /**
       * all btn style options
       */
      btnStyles: {
        light: 'light',
        transparent: 'transparent',
        outline: 'outline',
        background: 'background',
      },
      /**
       * all btn size options
       */
      btnSizes: {
        small: 'small',
        medium: 'medium',
      },
      /**
       * all mdi icon size options
       */
      mdiIconSizes: {
        xsmall: 'x-small',
        small: 'small',
        dense: 'dense',
        large: 'large',
        xlarge: 'x-large',
      },
    };
  },
  computed: {
    size() {
      if (this.btnSize === this.btnSizes.small) return '32px';
      return '40px';
    },
    /**
     * @returns if color theme is primary.
     */
    isPrimaryTheme() {
      return this.colorTheme.toLowerCase() === this.colorThemes.primary;
    },
    /**
     * @returns if color theme is secondary.
     */
    isSecondaryTheme() {
      return this.colorTheme.toLowerCase() === this.colorThemes.secondary;
    },
    /**
     * @returns if color theme is basic.
     */
    isBasicTheme() {
      return this.colorTheme.toLowerCase() === this.colorThemes.basic;
    },
    /**
     * @returns if color theme is error.
     */
    isErrorTheme() {
      return this.colorTheme.toLowerCase() === this.colorThemes.error;
    },
    /**
     * @returns the process circular loading color.
     */
    loadingColor() {
      if (!this.disabled && this.isLight && this.isPrimaryTheme) {
        return 'greenPrimary';
      }

      if (!this.disabled && this.isLight && this.isSecondaryTheme) {
        return 'bluePrimary';
      }

      if (!this.disabled && this.isLight && this.isErrorTheme) {
        return 'redPrimary';
      }

      if (!this.disabled && this.isLight && this.isBasicTheme) {
        return 'greyPrimary';
      }

      if (!this.disabled && !this.isBackground) {
        return this.buttonColor;
      }

      if (this.disabled && !this.isBackground) {
        return 'disabledPrimary';
      }

      return 'white';
    },
    /**
     * @returns button color based on color theme and btn style props.
     */
    buttonColor() {
      // SECONDARY COLORS
      if (this.isLight && this.isSecondaryTheme) {
        return 'rgba(75, 128, 233, 0.08)';
      }

      if (this.isSecondaryTheme) {
        return 'bluePrimary';
      }

      // ERROR COLORS
      if (this.isLight && this.isErrorTheme) {
        return 'rgba(255, 68, 91, 0.08)';
      }

      if (this.isErrorTheme) {
        return 'redPrimary';
      }

      // PRIMARY COLORS
      if (this.isPrimaryTheme && this.isLight) {
        return 'backgroundOverlay';
      }

      if (this.isPrimaryTheme) {
        return 'greenPrimary';
      }

      // BASIC THEME COLORS
      if (this.isBasicTheme && this.isLight) {
        return 'greyLight';
      }

      if (this.isBasicTheme) {
        return 'greyPrimary';
      }

      return this.colorTheme;
    },
    /**
     * @returns button classes based on given props
     */
    buttonClasses() {
      const classes = [];
      // adds btn size
      if (this.btnSize.toLowerCase()) {
        classes.push(this.btnSize.toLowerCase() + '-btn');
      }

      // adds 100% full width
      // if (this.hasFullWidth === true) {
      //   classes.push('full-width');
      // }

      // adds correct button color text
      if (!this.disabled) {
        if (this.isPrimaryTheme && this.isLight) {
          classes.push('greenPrimary--text');
        }

        if (this.isErrorTheme && this.isLight) {
          classes.push('redPrimary--text');
        }

        if (this.isSecondaryTheme && this.isLight) {
          classes.push('bluePrimary--text');
        }

        if (this.isBasicTheme && this.isLight) {
          classes.push('greyPrimary--text');
        }
      }
      // adds white text for all default backgrounds
      if (this.isBackground) {
        classes.push('white--text');
      }

      // adds class for disabled and btn style
      if (this.disabled) {
        classes.push('disabled-' + this.btnStyle.toLowerCase());
      }

      // adds class for btn style
      if (this.btnStyle.toLowerCase()) {
        classes.push('btn-' + this.btnStyle.toLowerCase());
      }

      return classes;
    },
    /**
     * @returns if btn style is transparent.
     */
    isTransparent() {
      return this.btnStyle.toLowerCase() === this.btnStyles.transparent;
    },
    /**
     * @returns if btn style has an outline/border.
     */
    hasOutline() {
      return this.btnStyle.toLowerCase() === this.btnStyles.outline;
    },
    /**
     * @returns if btn style is a lighter color.
     */
    isLight() {
      return this.btnStyle.toLowerCase() === this.btnStyles.light;
    },
    /**
     * @returns if btn style is the default background color.
     */
    isBackground() {
      return this.btnStyle.toLowerCase() === this.btnStyles.background;
    },
  },
};
</script>

<style lang="scss" scoped>
// MEW BUTTON STYLES
.v-application {
  .v-btn:not(.v-btn--round) {
    padding: 0;
    border-radius: 8px;
  }

  .v-btn.mew-icon-button {
    // BUTTON SIZES
    &.small-btn {
      padding: 8px;
    }

    &.medium-btn {
      padding: 0 20px;
    }
    
    /**
    * PRIMARY COLOR THEME 
    */
    // btn style: default background - active & hover states
    &.greenPrimary.btn-background.white--text:hover {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.08),
          rgba(255, 255, 255, 0.08)
        ),
        #05c0a5 !important;
    }

    &.greenPrimary.btn-background.white--text:active {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.2),
          rgba(255, 255, 255, 0.2)
        ),
        #05c0a5 !important;
    }

    // btn style: outline, transparent, light - active & hover states
    &.greenPrimary--text.btn-light:hover,
    &.greenPrimary--text.btn-outline:hover,
    &.greenPrimary--text.btn-transparent:hover {
      background: var(--v-greenLight-base) !important;
    }

    &.greenPrimary--text.btn-light:active,
    &.greenPrimary--text.btn-outline:active,
    &.greenPrimary--text.btn-transparent:active {
      background: var(--v-greenMedium-base) !important;
    }

    /**
    * SECONDARY COLOR THEME 
    */
    // btn style: default background - active & hover states
    &.bluePrimary.btn-background.white--text:hover {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.08),
          rgba(255, 255, 255, 0.08)
        ),
        #4b83e8 !important;
    }

    &.bluePrimary.btn-background.white--text:active {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.2),
          rgba(255, 255, 255, 0.2)
        ),
        #4b83e8 !important;
    }

    // btn style: light, outline, transparent - active & hover states
    &.bluePrimary--text.btn-light:active,
    &.bluePrimary--text.btn-outline:active,
    &.bluePrimary--text.btn-transparent:active {
      background: var(--v-blueMedium-base) !important;
    }

    &.bluePrimary--text.btn-light:hover,
    &.bluePrimary--text.btn-outline:hover,
    &.bluePrimary--text.btn-transparent:hover {
      background: var(--v-blueLight-base) !important;
    }

    /**
    * BASIC COLOR THEME 
    */
    // btn style: default background  - active & hover states
    &.greyPrimary.btn-background.white--text:hover {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.08),
          rgba(255, 255, 255, 0.08)
        ),
        #5a678a !important;
    }

    &.greyPrimary.btn-background.white--text:active {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.2),
          rgba(255, 255, 255, 0.2)
        ),
        #5a678a !important;
    }

    // btn style: outline, light, transparent - active & hover state
    &.greyPrimary--text.btn-light:hover,
    &.greyPrimary--text.btn-outline:hover,
    &.greyPrimary--text.btn-transparent:hover {
      background: var(--v-greyLight-base) !important;
    }

    &.greyPrimary--text.btn-light:active,
    &.greyPrimary--text.btn-outline:active,
    &.greyPrimary--text.btn-transparent:active {
      background: var(--v-greyMedium-base) !important;
    }

    /**
    * ERROR COLOR THEME 
    */
    // btn style: default background - active & hover states
    &.redPrimary.btn-background.white--text:hover {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.08),
          rgba(255, 255, 255, 0.08)
        ),
        #ff445b !important;
    }

    &.redPrimary.btn-background.white--text:active {
      background: linear-gradient(
          0deg,
          rgba(255, 255, 255, 0.2),
          rgba(255, 255, 255, 0.2)
        ),
        #ff445b !important;
    }

    // btn style: light, outline, transparent - active & hover states
    &.redPrimary--text.btn-light:hover,
    &.redPrimary--text.btn-outline:hover,
    &.redPrimary--text.btn-transparent:hover {
      background: var(--v-redLight-base) !important;
    }

    &.redPrimary--text.btn-light:active,
    &.redPrimary--text.btn-outline:active,
    &.redPrimary--text.btn-transparent:active {
      background: var(--v-redMedium-base) !important;
    }

    /**
    * DISABLED THEME
    */
    // btn style: outline
    &.disabled-outline {
      border: thin solid var(--v-disabledPrimary-base);
      color: var(--v-disabledPrimary-base) !important;
    }
    // btn style: transparent
    &.disabled-transparent {
      color: var(--v-disabledPrimary-base) !important;
    }
    // btn style: light
    &.disabled-light {
      background-color: var(--v-disabledLight-base) !important;
      color: var(--v-disabledPrimary-base) !important;
    }
    // btn style: default bg
    &.disabled-background {
      background-color: var(--v-disabledMedium-base) !important;
      color: var(--v-white-base) !important;
    }
  }
}
</style>
