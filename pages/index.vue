<template>
  <div>
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <section class="section">
            <div class="container">
              <div class="columns is-multiline gallery">
                <div
                  v-for="post in posts"
                  v-bind:key="post.slug"
                  class="column is-4"
                >
                  <div class="card">
                    <div class="card-image">
                      <figure class="image">
                        <datocms-image
                          :data="post.coverImage.responsiveImage"
                          pictureClass="my_image"
                        />
                      </figure>
                      <div class="card-content is-overlay is-clipped">
                        <a
                          :href="post.coverImage.url"
                          :data-caption="post.coverImage.alt"
                        >
                          <span class="tag"> #{{ post.title }} </span>
                        </a>
                      </div>
                    </div>
                  </div>
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
import baguetteBox from 'baguettebox.js'

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

    return toHead(this.site.favicon)
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
.tag:not(body) {
  background-color: transparent;
  font-family: 'Rubik', sans-serif;
  font-size: 3em;
  position: absolute;
  bottom: -10px;
  left: 5px;
  padding: 0;
}
</style>
