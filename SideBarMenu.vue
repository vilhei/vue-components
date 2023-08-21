<!-- eslint-disable no-unused-vars -->
<script setup lang="ts">
/**
 * Customizable toggleable side menu.
 */
import { computed, ref } from 'vue';
import { Color } from '@/services/util/CustomTypes';

/**
 * Props used to modify behaviour of SideBarMenu component
 */
interface Props {
  /** Controls menu location, defaults to left */
  position?: 'left' | 'right';
  /** Menu button location in pixels from left or right depending on position.<br> Default=15
   * @see position */
  horizontalPosition?: number;
  /**
   * Menu button location from top in pixels.<br> default=50
   */
  verticalPosition?: number;
  /**
   * Button color.
   * Can be defined as rgb,rgba or hex color. <br>
   * When using rgb/rgba use spaces after commas.
   * <br> Default=rgb(0, 140, 230) (blue) <br>
   * @see Color
   */
  buttonColor?: Color;
  /**
   * Controls whether the menu is on top of other elements (position=absolute) or not (position=relative)
   * <br>Default=true<br>
   */
  overlay?: boolean;
  /**
   * Controls whether the title is shown.<br>
   * Title is content inside slot #title (Default content is "Menu")
   * <br>Default=true<br>
   */
  showTitle?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  position: 'left',
  horizontalPosition: 15,
  verticalPosition: 50,
  buttonColor: 'rgb(0, 140, 230)',
  overlay: true,
  showTitle: true,
});

const showSideBarMenu = ref(false);

const horizontal = computed(() => `${props.horizontalPosition}px`);
const vertical = computed(() => `${props.verticalPosition}px`);

/**
 * Changes the animation direction (to left or to right) depending on positon prop.
 */
const translationName = computed(() => {
  if (props.position === 'left') {
    return 'side-bar-left';
  }
  return 'side-bar-right';
});

const menuCSSPosition = computed(() => (props.overlay ? 'absolute' : 'relative'));
</script>

<template>
  <label
    for="openSidebarMenu"
    class="sidebar-icon-toggle"
    :class="position === 'left' ? 'icon-left' : 'icon-right'">
    <input type="checkbox" id="openSidebarMenu" v-model="showSideBarMenu" />
    <div class="spinner diagonal part-1"></div>
    <div class="spinner horizontal"></div>
    <div class="spinner diagonal part-2"></div>
  </label>
  <Transition :name="translationName">
    <div
      class="side-bar-menu"
      :class="position === 'left' ? 'side-bar-menu-left' : 'side-bar-menu-right'"
      v-if="showSideBarMenu">
      <slot name="title" v-if="showTitle">
        <h3 class="menu-title">Menu</h3>
      </slot>
      <div class="side-bar-content">
        <slot> This is where you put wanted content for the menu </slot>
      </div>
    </div>
  </Transition>
</template>

<style scoped lang="css" src="../styles/shared-styles.css"></style>

<style scoped lang="css">
.menu-title {
  /* background-color: red; */
  padding: 0px;
  margin-top: 5px;
  margin-bottom: 0px;
}

.side-bar-menu {
  position: v-bind(menuCSSPosition);
  width: fit-content;
  height: 100dvh;
  background: linear-gradient(45deg, rgba(39, 39, 39, 0.75), rgb(15, 15, 15));
  z-index: 5;
  border-radius: 10px;
}

.side-bar-menu-left {
  left: 0px;
}

.side-bar-menu-right {
  right: 0px;
}

.side-bar-content {
  margin-left: 1rem;
  margin-right: 1rem;
  margin-top: 30px;
}

.side-bar-left-enter-active,
.side-bar-left-leave-active {
  transition: all 0.5s ease;
}

.side-bar-left-enter-from,
.side-bar-left-leave-to {
  /* opacity: 0; */
  scale: 0.5;
  transform: translateX(-150%);
}

.side-bar-right-enter-active,
.side-bar-right-leave-active {
  transition: all 0.5s ease;
}

.side-bar-right-enter-from,
.side-bar-right-leave-to {
  /* opacity: 0; */
  scale: 0.5;
  transform: translateX(150%);
}
/*
input[type="checkbox"]:checked ~ #sidebarMenu {
    transform: translateX(0);
} */

input[type='checkbox'] {
  transition: all 0.3s;
  box-sizing: border-box;
  display: none;
}
.sidebar-icon-toggle {
  transition: all 0.3s;
  box-sizing: border-box;
  cursor: pointer;
  position: absolute;
  z-index: 10;
  height: 100%;
  width: 100%;
  top: v-bind(vertical);
  height: 22px;
  width: 22px;
}

.icon-left {
  left: v-bind(horizontal);
}
.icon-right {
  right: v-bind(horizontal);
}
.spinner {
  transition: all 0.3s;
  box-sizing: border-box;
  position: absolute;
  height: 3px;
  width: 100%;
  background-color: v-bind(buttonColor);
}

.horizontal {
  transition: all 0.3s;
  box-sizing: border-box;
  position: relative;
  float: left;
  margin-top: 3px;
  background-color: v-bind(buttonColor);
}
.diagonal.part-1 {
  position: relative;
  transition: all 0.3s;
  box-sizing: border-box;
  float: left;
}
.diagonal.part-2 {
  position: relative;
  transition: all 0.3s;
  box-sizing: border-box;
  float: left;
  margin-top: 3px;
}

input[type='checkbox']:checked ~ .horizontal {
  transition: all 0.3s;
  box-sizing: border-box;
  opacity: 0;
}
input[type='checkbox']:checked ~ .diagonal.part-1 {
  transition: all 0.3s;
  box-sizing: border-box;
  transform: rotate(135deg);
  margin-top: 8px;
}
input[type='checkbox']:checked ~ .diagonal.part-2 {
  transition: all 0.3s;
  box-sizing: border-box;
  transform: rotate(-135deg);
  margin-top: -9px;
}
</style>
