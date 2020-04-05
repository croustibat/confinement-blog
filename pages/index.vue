<template>
  <div class="container">
    <header>
      <h1>#Restezchezvous</h1>
    </header>
    <div class="columns is-multiline gallery">
      <div v-for="post in posts" v-bind:key="post.slug" class="column is-4">
        <a
          :href="post.coverImage.url"
          :data-caption="post.title"
          class="image_link"
        >
          <datocms-image
            :data="post.coverImage.responsiveImage"
            pictureClass="my_image"
          />

          <div class="card-content">
            <span class="my_image_title"> #{{ post.title }} </span>
          </div>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import { request, gql, imageFields, seoMetaTagsFields } from '~/lib/datocms'
import { toHead } from 'vue-datocms'
import format from 'date-fns/format'
import parseISO from 'date-fns/parseISO'
import { fr } from 'date-fns/locale'
import baguetteBox from 'baguettebox.js'

export default {
  async asyncData({ params }) {
    const data = await request({
      preview: false,
      query: gql`
        {
          site: _site {
            globalSeo {
              siteName
              fallbackSeo {
                description
                title
                twitterCard
                image {
                  url
                }
              }
            }
            favicon: faviconMetaTags {
              ...seoMetaTagsFields
            }
          }

          posts: allPosts(first: 50, orderBy: date_ASC) {
            id
            title
            slug

            coverImage {
              url
              alt
              responsiveImage(
                imgixParams: { fit: fillmax, ar: "16:9", w: 860 }
              ) {
                ...imageFields
              }
            }
          }
        }
        ${imageFields}
        ${seoMetaTagsFields}
      `
    })

    return { ready: !!data, ...data }
  },
  methods: {
    formatDate(date) {
      if (!date) return
      return format(parseISO(date), 'PPP', { locale: fr })
    }
  },
  head() {
    if (!this.ready) {
      return
    }

    return {
      title: this.site.globalSeo.siteName,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.site.globalSeo.fallbackSeo.description
        },
        {
          hid: 'og-image',
          property: 'og-image',
          content: this.site.globalSeo.fallbackSeo.image.url
        },
        {
          hid: 'og-title',
          property: 'og-title',
          content: this.site.globalSeo.siteName
        },
        {
          hid: 'og-description',
          property: 'og-description',
          content: this.site.globalSeo.fallbackSeo.description
        }
      ]
    }
  },
  mounted() {
    baguetteBox.run('.gallery')
  }
}
</script>

<style lang="scss">
.card {
  background-color: transparent;
}
.is-4 {
  margin-bottom: 10px;
  &:nth-child(1n) {
    .my_image_title {
      background-color: #fce54d;
    }
    &:hover {
      .my_image_title {
        animation: slide 1.3s forwards cubic-bezier(0.215, 0.61, 0.355, 1);
        -webkit-animation: slide 1.3s forwards
          cubic-bezier(0.215, 0.61, 0.355, 1);
      }
    }
  }
  &:nth-child(2n) {
    .my_image_title {
      background-color: #d53c9f;
    }
    &:hover {
      .my_image_title {
        animation-delay: 300ms;
        animation: slide 1.3s forwards cubic-bezier(0.215, 0.61, 0.355, 1);
        -webkit-animation: slide 1.3s forwards
          cubic-bezier(0.215, 0.61, 0.355, 1);
      }
    }
  }
  &:nth-child(3n) {
    .my_image_title {
      background-color: #61c1f9;
    }
    &:hover {
      .my_image_title {
        animation-delay: 600ms;
        animation: slide 1.3s forwards cubic-bezier(0.215, 0.61, 0.355, 1);
        -webkit-animation: slide 1.3s forwards
          cubic-bezier(0.215, 0.61, 0.355, 1);
      }
    }
  }
}
.my_image_title {
  font-family: 'Rubik', sans-serif;
  font-size: 1.8em;
  position: absolute;
  bottom: 8px;
  left: -10px;
  padding: 5px 12px;
  color: white;
}
.image_link {
  position: relative;
}
@keyframes slide {
  0% {
    transform: rotate(0);
  }
  100% {
    transform: rotate(-8deg);
  }
}
</style>
