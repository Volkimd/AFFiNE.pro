<template lang="pug">
.template-share.flex.gap-10px
  el-tooltip(
    :content="copied ? 'Copied' : 'Copy link'"
    effect="light"
    :hide-after="50"
    popper-class="share-tooltip"
  )
    .handler( @click="handleLinkClick" )
      nuxt-icon( name="Link" )
  el-tooltip(
    content="Share to X"
    effect="light"
    :hide-after="50"
    popper-class="share-tooltip"
  )
    .handler( @click="handleTwitterClick" )
      nuxt-icon( name="Twitter2" )
  el-tooltip(
    content="Share to Reddit"
    effect="light"
    :hide-after="50"
    popper-class="share-tooltip"
  )
    .handler( @click="handleRedditClick" )
      nuxt-icon.text-size-17px( name="reddit" )
  el-tooltip(
    content="Share to email"
    effect="light"
    :hide-after="50"
    popper-class="share-tooltip"
  )
    .handler( @click="handleEmailClick" )
      nuxt-icon( name="share-email" )
</template>

<script lang="ts" setup>
import { PATH } from '~/utils/constants';
import { useClipboard } from '@vueuse/core';

const { copy, copied } = useClipboard();

const props = defineProps<{
  meta: TemplateContentFileMeta;
}>();

const templateLink = computed(() => {
  return `${PATH.SHARE_HOST}/templates/${props.meta.slug}`;
});

const shareText = computed(() => {
  return `${props.meta.title} ${templateLink.value}`;
});

const handleLinkClick = () => {
  copy(templateLink.value);
};

const handleTwitterClick = () => {
  window.open(
    `https://twitter.com/intent/tweet?text=${shareText.value}`,
    '_blank'
  );
};

const handleRedditClick = () => {
  window.open(`https://reddit.com/submit?url=${shareText.value}`, '_blank');
};

const handleEmailClick = () => {
  window.open(
    `mailto:?subject=${props.meta.title}&body=${templateLink.value}`,
    '_blank'
  );
};
</script>

<style lang="stylus">
.share-tooltip
  border-radius: 6px
  --el-border-color-light: #eee

.template-share
  font-size: 20px
  cursor pointer

  .handler
    color: hsla(0, 0%, 48%, 1)
    transition: color 0.3s

    &:hover
      color: var(--brand)
</style>
