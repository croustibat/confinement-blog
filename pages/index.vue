<template>
  <div>
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <section
            data-section-id="3"
            data-component-id="29e6_17_02_awz"
            data-category="gallery"
            class="section"
          >
            <div class="container">
              <h2 class="title has-text-centered" data-config-id="header">
                Jour 1
              </h2>
              <div class="columns is-multiline" data-config-id="gallery_02">
                <div
                  v-for="post in posts"
                  v-bind:key="post.slug"
                  class="column is-4"
                >
                  <a href="#">
                    <figure class="image">
                      <datocms-image :data="post.coverImage.responsiveImage" />
                    </figure>
                  </a>
                </div>
              </div>
            </div>
          </section>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import { request, gql, imageFields, seoMetaTagsFields } from '~/lib/datocms'
import { toHead } from 'vue-datocms'
import format from 'date-fns/format'
import parseISO from 'date-fns/parseISO'
import { fr } from 'date-fns/locale'

export default {
  async asyncData({ params }) {
    const data = await request({
      preview: false,
      query: gql`
        {
          site: _site {
            favicon: faviconMetaTags {
              ...seoMetaTagsFields
            }
          }

          posts: allPosts(first: 10, orderBy: _firstPublishedAt_DESC) {
            id
            title
            slug

            coverImage {
              responsiveImage(imgixParams: { fit: crop, ar: "16:9", w: 860 }) {
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

    return toHead(this.site.favicon)
  }
}
</script>
