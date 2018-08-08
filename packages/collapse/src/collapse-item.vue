<template>
  <div class="el-collapse-item" :class="{'is-active': isActive}">
    <div
      role="tab"
      :aria-expanded="isActive"
      :aria-controls="`el-collapse-content-${id}`"
      :aria-describedby ="`el-collapse-content-${id}`"
    >
      <div
        class="el-collapse-item__header"
        @click="handleHeaderClick"
        role="button"
        :id="`el-collapse-head-${id}`"
        tabindex="0"
        @keyup.space.enter.stop="handleEnterClick"
        :class="{
          'focusing': focusing,
          'is-active': isActive
        }"
        @focus="handleFocus"
        @blur="focusing = false"
      >
        <i
          v-if="arrowModel === 'double'"
          class="el-collapse-item__arrow-left el-icon-arrow-down"
          :class="{'is-active': isActive}">
        </i>
        <i
          :class="[isActive ? 'is-active' : '',
                  arrowPositionClass,
                  arrowDirectionClass]">
        </i>
        <slot name="title">{{title}}</slot>
      </div>
    </div>
    <el-collapse-transition>
      <div
        class="el-collapse-item__wrap"
        v-show="isActive"
        role="tabpanel"
        :aria-hidden="!isActive"
        :aria-labelledby="`el-collapse-head-${id}`"
        :id="`el-collapse-content-${id}`"
      >
        <div class="el-collapse-item__content">
          <slot></slot>
        </div>
      </div>
    </el-collapse-transition>
  </div>
</template>
<script>
  import ElCollapseTransition from 'element-ui-uwgd/src/transitions/collapse-transition';
  import Emitter from 'element-ui-uwgd/src/mixins/emitter';
  import { generateId } from 'element-ui-uwgd/src/utils/util';

  const arrowModelEnum = {
    DOUBLE: 'double',
    DOWN: 'down',
    RIGHT: 'right'
  };

  export default {
    name: 'ElCollapseItem',

    componentName: 'ElCollapseItem',

    mixins: [Emitter],

    components: { ElCollapseTransition },

    data() {
      return {
        contentWrapStyle: {
          height: 'auto',
          display: 'block'
        },
        contentHeight: 0,
        focusing: false,
        isClick: false
      };
    },

    inject: ['collapse'],

    props: {
      title: String,
      name: {
        type: [String, Number],
        default() {
          return this._uid;
        }
      },
      arrowModel: {
        type: String,
        default: arrowModelEnum.RIGHT
      }
    },

    computed: {
      isActive() {
        return this.collapse.activeNames.indexOf(this.name) > -1;
      },
      id() {
        return generateId();
      },
      arrowPositionClass() {
        return this.isShowDefaultArrowClass() ? 'el-collapse-item__arrow' : 'el-collapse-item__arrow-right';
      },
      arrowDirectionClass() {
        return this.isShowDefaultArrowClass() ? 'el-icon-arrow-right' : 'el-icon-arrow-down';
      }
    },

    methods: {
      isShowDefaultArrowClass() {
        return this.arrowModel === arrowModelEnum.RIGHT;
      },

      handleFocus() {
        setTimeout(() => {
          if (!this.isClick) {
            this.focusing = true;
          } else {
            this.isClick = false;
          }
        }, 50);
      },
      handleHeaderClick() {
        this.dispatch('ElCollapse', 'item-click', this);
        this.focusing = false;
        this.isClick = true;
      },
      handleEnterClick() {
        this.dispatch('ElCollapse', 'item-click', this);
      }
    }
  };
</script>
