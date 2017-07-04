<template>
  <div class="md-switch" :class="[$mdActiveTheme, switchClasses]">
    <div class="md-switch-container" @click.stop="toggleCheck">
      <div class="md-switch-thumb" :style="switchStyles">
        <md-ripple md-centered :md-active.sync="rippleActive" :md-disabled="disabled">
          <input type="checkbox" v-bind="{ id, name, disabled, required, value }">
        </md-ripple>
      </div>
    </div>

    <label :for="id" class="md-switch-label" v-if="$slots.default" @click.prevent="toggleCheck">
      <slot></slot>
    </label>
  </div>
</template>

<script>
  import MdComponent from 'core/MdComponent'
  import MdUuid from 'core/MdUuid'
  import MdRipple from 'components/MdRipple/MdRipple'

  export default new MdComponent({
    name: 'MdSwitch',
    components: {
      MdRipple
    },
    props: {
      model: [String, Number, Boolean, Array],
      value: {
        type: [String, Number, Boolean],
        default: 'on'
      },
      id: {
        type: String,
        default () {
          return 'md-switch-' + MdUuid()
        }
      },
      name: [String, Number],
      required: Boolean,
      disabled: Boolean
    },
    model: {
      prop: 'model',
      event: 'change'
    },
    data: () => ({
      rippleActive: false
    }),
    computed: {
      isSelected () {
        if (this.isArray) {
          return this.model.includes(this.value)
        }

        if (this.isBoolean) {
          return Boolean(this.model)
        }

        return this.model === this.value
      },
      isArray () {
        return Array.isArray(this.model)
      },
      isBoolean () {
        return typeof this.model === 'boolean'
      },
      switchClasses () {
        return {
          'md-checked': this.isSelected,
          'md-disabled': this.disabled,
          'md-required': this.required
        }
      },
      switchStyles () {
        return {

        }
      }
    },
    methods: {
      removeItemFromModel (newModel) {
        const index = newModel.indexOf(this.value)

        if (index !== -1) {
          newModel.splice(index, 1)
        }
      },
      handleArraySwitch () {
        const newModel = this.model

        if (!this.isSelected) {
          newModel.push(this.value)
        } else {
          this.removeItemFromModel(newModel)
        }

        this.$emit('change', newModel)
      },
      handleStringSwitch () {
        if (!this.isSelected) {
          this.$emit('change', this.value)
        } else {
          this.$emit('change', null)
        }
      },
      handleBooleanSwitch () {
        this.$emit('change', !this.isSelected)
      },
      toggleCheck () {
        if (!this.disabled) {
          this.rippleActive = true

          if (this.isArray) {
            this.handleArraySwitch()
          } else if (this.isBoolean) {
            this.handleBooleanSwitch()
          } else {
            this.handleStringSwitch()
          }
        }
      }
    }
  })
</script>

<style lang="scss">
  @import "~components/MdAnimation/variables";
  @import "~components/MdElevation/mixins";

  $md-switch-width: 34px;
  $md-switch-height: 14px;
  $md-switch-size: 20px;
  $md-switch-touch-size: 48px;

  .md-switch {
    width: auto;
    margin: 16px 16px 16px 0;
    display: inline-flex;
    position: relative;

    &:not(.md-disabled) {
      cursor: pointer;

      .md-switch-label {
        cursor: pointer;
      }
    }

    .md-switch-container {
      width: $md-switch-width;
      min-width: $md-switch-width;
      height: $md-switch-height;
      margin: 3px 0;
      display: flex;
      align-items: center;
      position: relative;
      border-radius: $md-switch-height;
      transition: $md-transition-stand;
    }

    .md-switch-thumb {
      @include md-elevation(1);
      width: $md-switch-size;
      height: $md-switch-size;
      position: relative;
      border-radius: 50%;
      transition: $md-transition-stand;

      &:before {
        width: $md-switch-touch-size;
        height: $md-switch-touch-size;
        position: absolute;
        top: 50%;
        left: 50%;
        z-index: 11;
        transform: translate(-50%, -50%);
        content: " ";
      }

      .md-ripple {
        width: $md-switch-touch-size !important;
        height: $md-switch-touch-size !important;
        top: 50% !important;
        left: 50% !important;
        position: absolute;
        transform: translate(-50%, -50%);
        border-radius: 50%;
      }

      input {
        position: absolute;
        left: -999em;
      }
    }

    .md-switch-label {
      height: $md-switch-size;
      padding-left: 16px;
      position: relative;
      line-height: $md-switch-size;
    }
  }

  .md-switch.md-checked {
    .md-switch-thumb {
      transform: translate3d(15px, 0, 0);
    }
  }

  .md-switch.md-required {
    label:after {
      position: absolute;
      top: 2px;
      right: 0;
      transform: translateX(calc(100% + 2px));
      content: "*";
      line-height: 1em;
      vertical-align: top;
    }
  }
</style>
