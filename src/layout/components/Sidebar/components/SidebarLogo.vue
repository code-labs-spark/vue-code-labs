<template>
  <div class="logo">
    <transition enter-active-class="animate__animated animate__fadeInLeft">
      <router-link :key="+collapse" class="wh-full flex-center" to="/">
        <img :src="logo" class="w20px h20px" />
        <span v-if="!collapse" class="title">
          <!-- {{ defaultSettings.title }} -->
          苍梧道隐
        </span>
      </router-link>
    </transition>
  </div>
</template>

<script lang="ts" setup>
import defaultSettings from "@/settings";
import logo from "@/assets/logo.webp";

defineProps({
  collapse: {
    type: Boolean,
    required: true,
  },
});
</script>

<style lang="scss" scoped>
.logo {
  width: 100%;
  height: $navbar-height;
  background-color: $sidebar-logo-background;

  .title {
    flex-shrink: 0; /* 防止容器在空间不足时缩小 */
    margin-left: 10px;
    font-size: 20px;
    font-weight: bold;
    color: white;
    background: linear-gradient(135deg, #ff4e50, #f9d423, #ff6a00, #d50032);
    background-clip: text;
    color: transparent;
    -webkit-background-clip: text; /* Safari */
  }
}

.layout-top,
.layout-mix {
  .logo {
    width: $sidebar-width;
  }

  &.hideSidebar {
    .logo {
      width: $sidebar-width-collapsed;
    }
  }
}
</style>
