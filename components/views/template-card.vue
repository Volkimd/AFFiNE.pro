<template lang="pug">
nuxt-link.template-card.flex.flex-col(
  :to="{ path: `/${isBlog ? 'blog' : 'templates'}/${meta.slug}`, meta: { source: 'list' } } "
)
  .card-cover-wrapper
    nuxt-img.card-cover(
      v-if="meta.thumbnail || meta.cover"
      :src="meta.thumbnail || meta.cover"
      :alt="meta.thumbnailAlt || meta.coverAlt || meta.title"
    )

  h3.card-title(
    :class="{ 'is-blog': isBlog }"
  ) {{ meta.title }}
  .card-desc(v-if="!isBlog") {{ meta.description }}
</template>

<script setup lang="ts">
const props = defineProps<{
  type: 'template' | 'blog'
  meta: TemplateContentFileMeta
}>()

const isBlog = computed(() => props.type === 'blog')
</script>

<style lang="stylus">
.template-card
  .card-cover-wrapper
    border: 1px solid #F3F3F3;
    border-radius: 8px;
    overflow: hidden
    transition: 268ms
    aspect-ratio: 328/210

  .card-cover
    object-fit: cover
    object-position: center top
    border-radius: 8px
    height: 100%
    width: 100%

  &:hover
    .card-cover-wrapper
      border-color: hsla(0, 0%, 80%, 1)

  .card-title
    padding: 0 6px
    margin-top: fv(16, 16)
    margin-bottom: 4px
    font-weight: 500;
    font-size: 18px;
    line-height: (24/18);
    letter-spacing: -0.02em;
    color: #141414;
    &:not(.is-blog)
      multiline-overflow(1)

  .card-desc
    padding: 0 6px
    font-size: 16px;
    line-height: 1.5;
    color: #7A7A7A;
    multiline-overflow(3)

</style>
