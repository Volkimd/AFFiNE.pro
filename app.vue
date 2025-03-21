<template lang="pug">
.app
  el-config-provider( :locale="locale" )
    nuxt-layout( keepalive )
      nuxt-page
</template>

<script setup>
import { ElConfigProvider } from 'element-plus';

import en from 'element-plus/es/locale/lang/en';

import 'element-plus/es/components/message/style/css';
import 'element-plus/theme-chalk/dark/css-vars.css';
import '~/styles/main.styl';

const locale = ref(en);

const i18n = useI18n();

useHead({
  htmlAttrs: {
    lang: i18n.locale.value || 'en',
  },
});
</script>

<style lang="stylus">
::selection
  background-color: var(--brand)
  color: white

body
  --bg: #F8F8F7
  margin: 0
  font-family: "Inter", "Roboto", "Helvetica", "Arial", sans-serif
  background: var(--bg)
  color: $primary100
  font-size: 1rem
  line-height: 1.5
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale

a
  text-decoration: none
  color: inherit
  transition: color 268ms

*, :before, :after
  box-sizing: border-box

// Common
.lottie-animation-container
  line-height: 1

.icon-gradient
  path
    fill: url(#brand-gradient) !important

.icon-stroke-gradient
  path
    stroke: url(#brand-gradient) !important

.nuxt-icon
  display: inline-flex
  vertical-align: middle
  line-height: 1

  &:not(:has(svg))
    min-width: 1em
    min-height: 1em

  svg
    margin: 0 !important

// Container
.base-container
  margin: 0 auto
  width: 100%

.container
  --width: 94.5rem
  margin: 0 auto
  width: 100%
  max-width: var(--width) !important

  &.is-fluid
    max-width: 100% !important

.limit-container
  --padding: 32px
  padding-left: var(--padding)
  padding-right: var(--padding)
  max-width: 1144px
  width: 100%
  margin: 0 auto

  @media $mediaInXS
    --padding: 20px

.md-container
  padding: 0 16px
  max-width: (1200px + 32px)
  width: 100%
  margin: 0 auto

.section-title
  margin: 0
  font-weight: 500;
  font-size: fluid-value(32, 60);
  line-height: (65/60);
  text-align: center
  letter-spacing: -0.068em;

.section-grad-title
  background-image: linear-gradient(91deg, #474747 20.12%, #000 55.27%, #474747 82.61%)
  background-clip: text;
  color: transparent

.section-grad2-title
  margin: 0
  font-weight: 800;
  font-size: fluid-value(20, 64);
  line-height: (77/64);
  background-image: linear-gradient(180deg, #1E96EB 0%, #1E96EB 100%);
  background-clip: text;
  color: transparent

  /html.dark &
    background-image: linear-gradient(180deg, #A8C3FF 0%, #417DFF 100%)

// Typography
.headline-2
  font-size: 24px;
  font-weight: 500;
  line-height: 1.333
  letter-spacing: -0.02em

.headline-6
  font-size: 14px;
  font-weight: 500;
  line-height: 20px;
  letter-spacing: -0.28px;

// Page
.page
  height: 100%
  display: flex
  flex-direction: column

  &-header
    min-height: 146px
    // background: #fafafa
    background: white
    padding: 24px 32px
    border-bottom: 1px solid var(--divider-color)
  &-content,
  &-container
    display: flex
    width: 100%
    // height: 100%
    overflow: hidden
    margin: 0 auto
    flex: 1
    // overflow-y: auto
  .is-scrollable
    overflow-y: auto
    height: 100%
    width: 100%
    padding-bottom: 44px

  &-content-main
    flex: 1
    padding-left: 44px
    padding-right: 44px
    padding-top: 44px
    padding-bottom: 60px
    min-height: 100%
    overflow-y: auto;

  &-container
    padding-top: 0
    padding-bottom: 0
    width: 100%

  &-title
    // font-weight: 500
    font-size: 36px
    margin-bottom: 10px

  // Form
  .input-title
    .el-input__wrapper
      box-shadow: none
      padding: 0
      font-weight: 700;
      font-size: 24px;

    .el-input__inner
      color: black

  .el-textarea__inner
    font-size: 16px
    line-height: 24px
    color: black
    padding: 12px 16px

  .input-content

    &.el-textarea.is-disabled
      .el-textarea__inner
        background: none

    .el-textarea__inner
      box-shadow: none
      padding: 0

  // Actions
  .round-icon-button
    border: none
    padding: 0
    font-size: 32px
    width: 32px
    height: 32px
</style>
