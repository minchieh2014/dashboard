<script>
import { KEYMAP } from '@/store/prefs';

export default {
  props: {
    value: {
      type:     String,
      required: true,
    },
    options: {
      type:    Object,
      default: () => {}
    }
  },

  data() {
    return { loaded: false };
  },

  computed: {
    combinedOptions() {
      const theme = this.$store.getters['prefs/theme'];
      const keymap = this.$store.getters['prefs/get'](KEYMAP);

      const out = {
        // codemirror default options
        tabSize:                 2,
        indentWithTabs:          false,
        mode:                    'yaml',
        keyMap:                  keymap,
        theme:                   `base16-${ theme }`,
        lineNumbers:             true,
        line:                    true,
        styleActiveLine:         true,
        lineWrapping:            true,
        foldGutter:              true,
        styleSelectedText:       true,
        showCursorWhenSelecting: true,
      };

      Object.assign(out, this.options);

      return out;
    },
  },

  mounted() {
    import('@/plugins/codemirror').then(() => {
      this.loaded = true;
    });
  },

  methods: {
    onReady(cm) {
      this.$emit('onReady', cm);
    },

    onInput(newCode) {
      this.$emit('onInput', newCode);
    },

    onChanges(cm, changes) {
      this.$emit('onChanges', cm, changes);
    },
  }
};
</script>

<template>
  <client-only placeholder=" Loading...">
    <div class="code-mirror">
      <codemirror
        v-if="loaded"
        :value="value"
        :options="combinedOptions"
        @ready="onReady"
        @input="onInput"
        @changes="onChanges"
      />
      <div v-else>
        Loading...
      </div>
    </div>
  </client-only>
</template>

<style lang="scss">
  .code-mirror .vue-codemirror .CodeMirror {
    height: initial;
    background: none
  }
</style>
