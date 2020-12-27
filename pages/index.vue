<template>
  <div>
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <div v-for="category in categorys.slice(0, 2)" v-bind:key="category.slug">
            <div class="columns">
              <div class="column is-8 is-offset-2">
                <figure class="image">
                  <datocms-image :data="category.coverImage.responsiveImage" />
                </figure>
              </div>
            </div>

            <section class="section">
              <div class="columns">
                <div class="column is-8 is-offset-2">
                  <div class="content is-medium">
                    <h1 class="title">
                      <nuxt-link :to="`/categorys/${category.slug}`">{{
                        category.title
                      }}</nuxt-link>
                    </h1>
                    <div v-html="category.excerpt" />
                  </div>
                </div>
              </div>
            </section>

            <div class="is-divider" />
          </div>
        </div>
      </div>
    </section>

    <!-- newsletter -->
    <section class="section">
      <div class="columns">
        <div class="column is-10 is-offset-1">
          <div class="container has-text-centered is-fluid">
            <div class="hero is-light">
              <div class="hero-body">
                <h2 class="title is-4">Sign up for our newsletter</h2>
                <div class="column is-6 is-offset-3">
                  <div class="field has-addons has-addons-centered">
                    <div class="control is-expanded">
                      <input
                        class="input"
                        type="text"
                        placeholder="Email address"
                      />
                    </div>
                    <div class="control">
                      <a class="button is-info">
                        Subscribe
                      </a>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Articles -->

    <section class="hero ">
      <div class="hero-body">
        <div class="container">
          <div
            v-for="group in Math.ceil((categorys.length - 2) / 2)"
            v-bind:key="group"
          >
            <section class="section">
              <div class="columns is-variable is-8">
                <div
                  v-for="(category, index) in categorys.slice(group * 2, group * 2 + 2)"
                  v-bind:key="category.slug"
                  :class="['column is-5', index === 0 && 'is-offset-1']"
                >
                  <div class="content is-medium">
                    <h1 class="title has-text-black is-3">
                      <nuxt-link :to="`/categorys/${category.slug}`">{{
                        category.title
                      }}</nuxt-link>
                    </h1>
                    <div class="has-text-dark" v-html="category.excerpt" />
                  </div>
                </div>
              </div>
            </section>

            <div class="is-divider" />
          </div>
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

export default {
  async asyncData({ params }) {
    const data = await request({
      query: gql`
        {
          site: _site {
            favicon: faviconMetaTags {
              ...seoMetaTagsFields
            }
          }

          categorys: allCategorys(first: 10) {
            id
            title
            slug
            excerpt
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
      return format(parseISO(date), 'PPP')
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
