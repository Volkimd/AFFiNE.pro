<template lang="pug">
.page.page-templates
  .section.section-hero
    .limit-container
      h1.section-title {{ templateHero.cateTitle || 'Start with Template' }}

      template-tags-list(
        :tags="templateTags"
        :cates="templateCates"
      )

    .limit-container.narrow-container
      template-hero-card(
        v-if="templateHero"
        :meta="templateHero"
      )

  .section.section-main
    .limit-container.narrow-container
      .template-list
        template-card(
          v-for="meta in templateList"
          :key="meta.slug"
          :meta="meta"
        )

      template-intro(
        :md="templateHero?.intro || ''"
      )
</template>

<script lang="ts" setup>
import { primaryAPI } from '~/apis';
import { useTemplateMetas } from '~/services/templates/useTemplateMetas';
import { getTemplateCateMeta, getTemplateCateSchema } from '~/utils/template';

const store = useStore();
const url = useRequestURL();
const route = useRoute();
const asyncOptions = reactive({
  isLoading: true,
  isError: false,
  isInited: false,
});

const loadData = async () => {
  try {
    asyncOptions.isLoading = true;
    await primaryAPI.getTemplates();
  } catch (error) {
    asyncOptions.isError = true;
  }
  asyncOptions.isLoading = false;
};

const templateMetas = computed(() =>
  useTemplateMetas(store.templates, {
    tag: route.params.slug as string,
  })
);

const templateHero = computed(() => {
  return templateMetas.value.featuredMeta;
});

const templateList = computed(() => {
  return templateMetas.value.filteredMetas;
});

const templateTags = computed(() => {
  return templateMetas.value.tags;
});

const templateCates = computed(() => {
  return templateMetas.value.cates;
});

await loadData();

const pageMeta = computed(() => ({
  title: templateHero.value?.cateTitle,
  meta: getTemplateCateMeta(templateHero.value),
  script: [
    {
      hid: 'breadcrumbs-json-ld',
      type: 'application/ld+json',
      textContent: JSON.stringify(getTemplateCateSchema(templateHero.value)),
    },
  ],
}));

useHead(pageMeta);
</script>

<style lang="stylus">
.page.page-templates
  padding-top: fv(60, 100)

  .narrow-container
    max-width: 1100px

  .section-hero
    margin-bottom: fv(4, 20)

    .section-title
      font-weight: 500;
      font-size: fv(36, 60);
      line-height: (70/60);
      letter-spacing: -0.05em;
      background: linear-gradient(91.4deg, #474747 20.12%, #000000 55.27%, #474747 82.61%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      text-fill-color: transparent;
      margin-bottom: fv(32, 36)

  .template-list
    display: grid
    column-gap: fv(24, 32)
    row-gap: fv(24, 32)
    grid-template-columns: 1fr 1fr 1fr

    @media (max-width: 920px)
      grid-template-columns: 1fr 1fr

    @media $mediaInXS
      grid-template-columns: 1fr
</style>
