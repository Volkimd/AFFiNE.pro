<template lang="pug">
.page.page-ai

  .section.section-hero
    .limit-container
      ai-hero

  .section.section-tri-slides
    .limit-container
      ai-tri-slides

  .section.section-feature-tarot
    .limit-container
      h2.section-subtitle
        | What AFFiNE AI Can Do:

      ai-feature-tarot

      .coming-soon-tips.text-center AI with Database / Workspace will be available soon.

  .section.section-qna
    .limit-container
      h2.section-subtitle
        | Question & Answers

      ai-question-and-answers

  .section.section-get-started-with
    .limit-container
      h2.section-subtitle
        | Get Started&nbsp;
        span.muted with AFFiNE AI

      pricing-ai-section

  .preload-images
    img( src="/ai/slide-write.png" )
    img( src="/ai/slide-draw.png" )
    img( src="/ai/slide-present.png" )

</template>

<script lang="ts" setup>
const meta = {
  title: 'AFFiNE AI – The Leading Canvas AI for Smarter Note Taking and Collaboration',
  description: 'Explore AFFiNE AI, the premier Canvas AI tool for seamless planning, brainstorming, and team collaboration. Enhance creativity, streamline workflows, and organize projects with ease using AFFiNE AI\'s intuitive and flexible platform.',
}
useHead({
  title: meta.title,
  meta: [
    { name: 'description', content: meta.description },
    { name: 'og:description', content: meta.description },
    { name: 'og:title', content: meta.title },
    { name: 'twitter:description', content: meta.description },
    { name: 'twitter:title', content: meta.title },
  ],
})

definePageMeta({
  keepalive: false
})
</script>

<style lang="stylus">
.page.page-ai
  padding-bottom: 40px

  .preload-images
    position: absolute
    overflow: hidden
    left: -9999px
    top: -9999px
    height: 1px
    width: 1px

  .section
    .section-subtitle
      font-weight: 500;
      font-size: fluid-value(24, 32);
      line-height: (43/36);
      letter-spacing: -0.03em;
      color: #000;
      margin-top: fluid-value(44, 80);
      margin-bottom: fluid-value(20, 40);

      @media $mediaInMobile
        letter-spacing: -0.02em;

      .muted
        color: #8E8D91

  .section-feature-tarot
    .coming-soon-tips
      margin-left: 20px
      margin-right: 20px
      margin-top: 20px
      font-size: 15px
      line-height: (24/15)
      color: #8E8D91
</style>
