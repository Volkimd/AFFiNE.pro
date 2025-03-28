<template lang="pug">
.release-white-card.inline-flex.flex-col(
  ref="el"
)
  hover-spotlight-card.card-wrapper.inline-flex.flex-col.items-center(
    :lightSize="340"
  )
    .card-title
      | {{ title }}
      .title-glow( v-if="isShowTitleGlow" )
    .update-frequency( v-html="updateFrequency" )
    svg-icon-drawing(  v-if="!$device.isMobile" :isShow="hasAssets && !isOutside" )
      nuxt-icon.download-drawing-line(
        filled name="download-drawing-line"
      )
    //- .release-tag( v-if="hasAssets" ) {{ tag_name.substring(1) }}
    .card-icon(
      :style="{ backgroundImage: `url(${icon})` }"
    )
    .card-desc.text-center( v-html="desc" )
    brand-glow-button(
      @click="() => handleDownloadClick(defaultAsset, title)"
      :needShadow="!tag_name?.includes('canary')"
      :disabled="!hasAssets"
    )
      | {{ hasAssets ? $t('download') : $t(isObsolete ? 'outdated' : 'comingSoon') }}

    .publish-date( v-if="defaultAsset && !isObsolete" )
      | {{ $t('latestVersion') }}
      span.fw-500 {{ publishDate }}

    .other-version( v-if="hasAssets" )
      .other-version-title.flex.items-center(
        :class="{ 'is-active': isShowOtherVersion }"
        @click="() => isShowOtherVersion = !isShowOtherVersion"
      )
        nuxt-icon( name="ArrowRightSmall" )
        | {{ $t('downloadPage.otherVersion') }}

      el-collapse-transition
        .expand-container( v-show="isShowOtherVersion" )
          template( v-if="assetsMap.mac.length" )
            .other-version-title {{ $t('downloadPage.otherVersionFor') }} {{ Platform.Mac }}
            ul
              li( v-for="asset in assetsMap.mac" )
                nuxt-link( :to="asset.url" ) {{ asset.name }}

          template( v-if="assetsMap.windows.length" )
            .other-version-title  {{ $t('downloadPage.otherVersionFor') }} {{ Platform.Win }}
            ul
              li( v-for="asset in assetsMap.windows" )
                nuxt-link( :to="asset.url" ) {{ asset.name }}

          template( v-if="assetsMap.linux.length" )
            .other-version-title  {{ $t('downloadPage.otherVersionFor') }} {{ Platform.Linux }}
            ul
              li( v-for="asset in assetsMap.linux" )
                nuxt-link( :to="asset.url" ) {{ asset.name }}

  .info-tips.mt-14px( v-if="tips" ) {{ tips }}

  download-canary-modal(
    v-if="title == 'Canary'"
    v-model="isShowCanaryModal"
    :releases="releases"
    :defaultAsset="defaultAsset"
  )

</template>

<script setup lang="ts">
import { useMouseInElement, useDateFormat } from '@vueuse/core';

const el = ref(null);
const isShowCanaryModal = ref(false);
const { isOutside } = useMouseInElement(el, { handleOutside: false });

const props = defineProps<{
  isLatest: boolean;
  isShowTitleGlow: boolean;
  title: string;
  desc: string;
  updateFrequency: string;
  icon: string;
  tips?: string;
  tag_name: string;
  prerelease: boolean;
  published_at: string;
  releases: Record<string, Release>;
  assets: Asset[];
}>();

const Platform = {
  Mac: 'macOS',
  MacArm: 'Apple Silicon (Arm64)',
  Win: 'Windows',
  Linux: 'Linux',
};

const mixpanel = useMixpanel();
const $device = useDevice();
const isObsolete = computed(() => !props.isLatest && !props.isShowTitleGlow);
const publishDate = useDateFormat(
  new Date(props.published_at || Date.now()),
  'MMM DD, YYYY'
);
const hasAssets = computed(
  () => !isObsolete.value && !!props.tag_name && props.assets.length > 0
);
const isArm64 = ref(false);
const isShowOtherVersion = ref(false);

const isWinAsset = (asset: Asset) => asset.name.includes('windows');
const isWinExeAsset = (asset: Asset) =>
  isWinAsset(asset) && asset.name.endsWith('-x64.exe');
const isWinArmAsset = (asset: Asset) =>
  isWinAsset(asset) && asset.name.endsWith('-arm64.exe');
const isMacAsset = (asset: Asset) => asset.name.includes('macos');
const isMacArmAsset = (asset: Asset) =>
  isMacAsset(asset) && asset.name.includes('arm64');
const isLinuxAsset = (asset: Asset) => asset.name.includes('linux');

const defaultAssetPlatformName = computed(() => {
  const asset = defaultAsset.value;
  if (!asset) return;

  if (isMacArmAsset(asset)) return Platform.MacArm;
  if (isMacAsset(asset)) return Platform.Mac;
  if (isWinAsset(asset)) return Platform.Win;
  if (isLinuxAsset(asset)) return Platform.Linux;
});

const defaultAsset = computed(() => {
  let asset;
  const assets = props.assets || [];

  if ($device.isMobileOrTablet) {
    asset = assets.find((el) => isWinExeAsset(el));
  } else if ($device.isMacOS) {
    asset = isArm64.value
      ? assets.find((el) => isMacArmAsset(el))
      : assets.find((el) => !isMacArmAsset(el) && isMacAsset(el));
  } else if ($device.isWindows) {
    asset = isArm64.value
      ? assets.find((el) => isWinArmAsset(el))
      : assets.find((el) => isWinExeAsset(el));
  }

  if (!asset) {
    asset = assets[0];
  }

  return asset;
});

const assetsMap = computed(() => {
  let mac: Asset[] = [];
  let windows: Asset[] = [];
  let linux: Asset[] = [];

  props.assets.map((asset) => {
    if (isMacAsset(asset)) {
      return mac.push(asset);
    }
    if (isWinAsset(asset)) {
      return windows.push(asset);
    }
    if (isLinuxAsset(asset)) {
      return linux.push(asset);
    }
  });

  mac = mac.filter((el) => el.name !== defaultAsset.value.name);
  windows = windows.filter((el) => el.name !== defaultAsset.value.name);
  linux = linux.filter((el) => el.name !== defaultAsset.value.name);

  return {
    mac,
    windows,
    linux,
  };
});

const handleDownloadClick = (asset: Asset, type?: string) => {
  if (type === 'Canary') {
    isShowCanaryModal.value = true;
    return;
  }
  if (!asset) return;
  mixpanel.track('Button', { resolve: type });
  var link = document.createElement('a');
  link.download = asset.name;
  link.href = asset.url;
  link.click();
};

onBeforeMount(async () => {
  const { architecture } = await navigator.userAgentData?.getHighEntropyValues([
    'architecture',
  ]);
  if (architecture?.includes('arm')) {
    isArm64.value = true;
  }
});
</script>

<style lang="stylus">
.release-white-card
  transform: perspective(800px) scale(var(--scale, 1))
  transform-style: preserve-3d

  .card-wrapper
    width: 100%
    background-color: white;
    border: 1px solid var(--black-1, rgba(0, 0, 0, 0.10));
    box-shadow: 0px 1px 6px 0px rgba(0, 0, 0, 0.08);
    border-radius: 16px;
    padding: 30px
    gap: fluid-value(16, 18)
    --light-color:rgba(#1E96EB, 0.1)
    background-image: url(@/assets/download/icon-bg.svg)
    background-size: 347px 545px
    background-position: center 4px
    background-repeat: no-repeat

    @media $mediaInMobile
      background-position: center 0px

    .update-frequency
      margin-top: -1em
      font-weight: 500;
      font-size: 16px

    .svg-icon-drawing
      position absolute
      top: 84px

      svg
        width: 151px
        height: 8px

    .release-tag
      margin: -1em
      font-size: fluid-value(11, 14)

    .card-title
      position: relative
      font-weight: 500;
      font-size: 32px
      line-height: 1.2
      letter-spacing: -0.04em
      color: black

      .title-glow
        position: absolute
        width: 200%
        aspect-ratio: 1/0.07
        border-radius: 45px
        background: rgba(255, 255, 255, 0.80)
        filter: blur(27.5px)
        left: 50%
        top: 50%
        transform: translate3d(-50%, -50%, 0)

    .card-icon
      width: 134px
      aspect-ratio: 1/1
      background-size: contain

    .card-desc
      min-height: 120px
      white-space: pre-line
      font-size: fluid-value(14, 16)
      line-height: (24/16);
      margin-bottom: 10px

      b
        margin-top: -4px
        display: block
        border-radius: 4px
        padding: 4px 16px
        font-weight: 400
        color: #1E96EB !important
        font-size: 13px
        background: var(--brand-secondary, #EFFAFF);

  .brand-glow-button
    padding: 0 40px
    min-width: 232px

  .platform-name
    margin-top: -0.3em
    font-size: 14px;
    line-height: 17px;

    @media $mediaInXS
      margin-top: -0.5em

  .publish-date
    font-weight: 400;
    font-size: 12px;
    line-height: 15px;
    margin-top: -0.5em

  .other-version
    width: 100%
    padding: 0 12px

    @media $mediaInXS
      font-size: 12px
      padding: 0 0

    ul
      padding-left: 24px
      margin: 4px

      a
        line-height: 220.02%;
        text-decoration: underline
        text-underline-offset: 3px
        &:hover
          opacity: 0.85

  .other-version-title
    height: 32px
    font-weight: 500;
    font-size: 16px;
    line-height: 220.02%;
    color: var(--primary-gray)
    gap: 14px
    cursor pointer

    .nuxt-icon
      transition: 368ms
      transform: rotate(180deg)

    &.is-active
      .nuxt-icon
        transform: rotate(270deg)

  .info-tips
    font-weight: 500;
    font-size: 16px;
    line-height: (20/16);
    padding-left: 24px
    position relative

    &:before
      content: ''
      display: inline-block
      position absolute
      top: 8px
      left: 10px
      width: 4px
      height: 4px
      border-radius: 50%
      background-color: currentColor
      margin-right: 10px
</style>
