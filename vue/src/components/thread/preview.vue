<style lang="scss">
@import 'mixins';
@import 'boxes';

// .thread-preview {
//   @include md-body-1;
//   @include listTransition;
//   min-height: $boxMediumSize + ($thinPaddingSize * 2);
//   border-bottom: 1px solid $border-color;
//   &:hover .thread-preview__actions { opacity: 1; }
//   &.loading-content { padding: 10px; }
//   position: relative;
// }

// .thread-preview__link {
//   display: flex;
//   justify-content: space-between;
//   padding: $thinPaddingSize;
//   color: $primary-text-color;
//   text-decoration: none;
//
//   &.thread-preview__link--unread {
//     border-left: 2px solid;
//     padding-left: $thinPaddingSize - 2px;
//   }
// }

.thread-preview__status-icon {
  padding: 4px 8px;
}

.thread-preview__pin {
  width: 32px;
  font-size: 20px;
  color: $grey-on-white;
  text-align: center;
}

.thread-preview__actions {
  position: absolute;
  display: flex;
  right: 40px;
  transition: opacity 0.25s ease-in-out;
  top: 0;
  opacity: 0;
  margin: $thinPaddingSize $cardPaddingSize;
  .md-button {
    align-items: center;
    display: flex;
    justify-content: center;
    min-width: 40px;
    .mdi { font-size: 16px; }
  }
}

.thread-preview__mute,
.thread-preview__unmute { margin-left: 8px }

// .thread-preview__group-name{
//   @include md-caption;
//   @include truncateText;
//   padding-left: $thinPaddingSize;
//   color: $grey-on-white;
// }

.thread-preview__unread-count {
  color: $grey-on-white;
  min-width: 33px;
  padding-left: 5px;
}

.thread-preview--unread {
  font-weight: 500;
}

.thread-preview__position-icon-container {
  width: 23px;
  height: 23px;
  position: absolute;
  left: 15px;
  top: 43px;
  background-color: white;
  border-radius: 100%;
  box-shadow: 0 2px 1px rgba(0,0,0,.15);
}

.thread-preview__position-icon {
  background-repeat: no-repeat;
  height: 21px;
  margin: 1px 0 0 1px;
  width: 21px;
}

.thread-preview__undecided-icon {
  font-size: 14px;
  line-height: 24px;
  text-align: center;
}

</style>

<script lang="coffee">
import ThreadService from '@/shared/services/thread_service'
import urlFor        from '@/mixins/url_for'

export default
  mixins: [urlFor]
  props:
    thread: Object
  data: ->
    dthread: @thread
  methods:
    dismiss: -> ThreadService.dismiss(this.thread)
    muteThread: -> ThreadService.mute(this.thread)
    unmuteThread: -> ThreadService.unmute(this.thread)
  computed:
    threadUrl: -> "/d/#{this.thread.key}"
</script>

<template lang="pug">
v-list-tile.thread-preview(:to='urlFor(thread)', md-colors="{'border-color': 'primary-500'}", :class="{'thread-preview__link--unread': thread.isUnread()}")
  v-list-tile-avatar
    user-avatar(v-if='!thread.activePoll()', :user='thread.author()', size='medium')
    poll-common-chart-preview(v-if='thread.activePoll()', :poll='thread.activePoll()')
  v-list-tile-content
    v-list-tile-title
      span.thread-preview__title(:class="{'thread-preview--unread': thread.isUnread() }") {{thread.title}}
      span.thread-preview__unread-count(v-if='thread.hasUnreadActivity()') ({{thread.unreadItemsCount()}})
    v-list-tile-sub-title
      | {{ thread.group().fullName }} ·
      time-ago(:date='thread.lastActivityAt')
      .lmo-badge.lmo-pointer(v-if='thread.closedAt', md-colors="{color: 'warn-600', 'border-color': 'warn-600'}", v-t="'common.privacy.closed'")
    .thread-preview__pin.thread-preview__status-icon(v-if='thread.pinned', :title="$t('context_panel.thread_status.pinned')")
      v-icon mdi-pin
      //- .thread-preview__actions.lmo-hide-on-xs(v-if='thread.discussionReaderId')
      //-   button.md-raised.thread-preview__dismiss(@click='dismiss()', :disabled='!thread.isUnread()', :class='{disabled: !thread.isUnread()}', :title="$t('dashboard_page.dismiss')")
      //-     .mdi.mdi-check
      //-   button.md-raised.thread-preview__mute(@click='muteThread()', v-show='!thread.isMuted()', :title="$t('volume_levels.mute')")
      //-     .mdi.mdi-volume-mute
      //-   button.md-raised.thread-preview__unmute(@click='unmuteThread()', v-show='thread.isMuted()', :title="$t('volume_levels.unmute')")
      //-     .mdi.mdi-volume-plus
</template>
