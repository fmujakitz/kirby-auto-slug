<template>
  <k-field
    name="input-field"
    :label="label"
    >
    <k-input
      name="text"
      type="text"
      theme="field"
      ref="textarea"
      v-bind:value="value"
      :buttons="false"
      :placeholder="placeholder"
      :disabled="true"
    />
  </k-field>
</template>


<script>

export default {
  /** put your view logic here **/
  props: {
    label: String,
    value: String,
    uses: Array,
    shows: { type: Array, default: [] },
    updateTitle: Boolean,
    updateSlug: Boolean
  },
  methods: {
    getInfo(keys) {
      const [ original, current ] = ['originals', 'changes']
        .map(k => this.$store.getters['content/' + k]())

      const data = keys.map(k => current[k] !== undefined
        ? current[k] 
        : original[k]
      )
      
      return data.filter(Boolean).join(' ')
    },
    getUses() {
      return this.getInfo(this.uses)
    },
    getShows() {
      return this.getInfo(this.shows) || this.getUses()
    },
    generate() {
      return this.$helper.slug(this.getUses())
    },
    save(slug) {
      this.$emit('input', slug)
    },
    exchange() {     
      const id = this.$store.getters['content/id']()

      const slug = {
        path: id.replace('pages/', ''),
        current: id.split('/').reverse()[0],
        next: this.value
      }

      if (slug.current === slug.next) {
        this.$store.dispatch('notification/success', ':)');
        return
      }

      this.$api.pages
      .slug(slug.path, slug.next)
      .then(page => {

        const title = this.getShows()
        if (this.updateTitle && page.title !== title) {
          this.$api.pages.title(page.id, title)
        }

        const next = 'pages/' + page.id
        this.$store.dispatch('content/move', [id, next])

        const payload = {
          message: ':)',
          event: 'page.changeSlug'
        };

        const { current } = this.$store.state.languages
        const { path } = this.$route.params

        // if in PageView and default language, redirect
        const inPageView = path && slug.path === path.replace(/\+/g, '/')
        const defaultLang = !current || current.default === true

        if (inPageView && defaultLang) {
          payload.route = this.$api.pages.link(page.id);
          delete payload.event;
        }

        this.success(payload)
      })
    },
    success({ route, message, event }) {
      if (route) this.$router.push(route);
      if (message) this.$store.dispatch('notification/success', message);
      if (event) this.$events.$emit(event);

      this.$emit('success');
    }
  },
  mounted() {
    const { value } = this
    const current = this.generate()

    if (value !== current) {
      this.save(current)
      return
    }

    if (this.updateSlug) this.exchange()
  },
  computed: {
    hasChanges() {
      return this.generate()
    },
  },
  watch: {
    hasChanges(current, previous) {
      // if changes happen, do stuff
      if (previous !== current) this.save(current)
    }
  },
  created() {
    this.$events.$on('model.update', this.exchange)
  },
  destroyed() {
    this.$events.$off('model.update')
  }
};
</script>

<style lang="scss">
/** put your css here **/
</style>
