<template>
  <div class="modal show text-left">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" @click="$emit('close', isChanged)">&times;</button>
          <h4 class="modal-title">
            {{ $t(`opt:match_pattern_${liststate}`) }}
            <small> - <a :href="$t(`opt:match_pattern_url`)" target="_blank">{{ $t(`opt:example`) }}</a></small>
          </h4>
        </div>
        <div class="modal-body">
          <h1 class="text-center" v-if="patternlist.length <= 0">Empty</h1>
          <div class="form-group" v-for="(item, i) in patternlist" :key="i" :class="{ 'has-error': item.haserror }">
            <input type="text" class="form-control" :placeholder="$t('opt:match_pattern_placeholder')"
              @input="inputPattern(item, $event.target.value)"
              :value="item.pattern"
            >
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-success" :disabled="haserror" @click="addItem">{{ $t('opt:dicts_btn_add') }}</button>
          <button type="button" class="btn btn-primary" :disabled="haserror" @click="save">{{ $t('confirm') }}</button>
          <button type="button" class="btn btn-default" @click="$emit('close', isChanged)">{{ $t('cancel') }}</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { matchPatternToRegExpStr } from '@/_helpers/matchPatternToRegExpStr'

export default {
  props: ['list', 'liststate'],
  data () {
    return {
      isChanged: false,
      patternlist: JSON.parse(JSON.stringify(
        this.list.map(([regexp, pattern]) => ({
          pattern,
          regexp,
          haserror: false,
        }))
      ))
    }
  },
  computed: {
    haserror () {
      return this.patternlist.some(({ haserror }) => haserror)
    }
  },
  methods: {
    addItem () {
      this.patternlist.push({
        pattern: '',
        regexp: '',
        haserror: false,
      })
    },
    inputPattern (item, value) {
      this.isChanged = true
      item.pattern = value
      const regexp = matchPatternToRegExpStr(value)
      if (regexp) {
        item.regexp = regexp
        item.haserror = false
      } else {
        item.haserror = true
      }
    },
    save () {
      this.$emit(
        'save',
        this.patternlist
          .filter(({ regexp, pattern }) => pattern && regexp)
          .map(({ regexp, pattern }) => [regexp, pattern]),
      )
    }
  },
}
</script>
