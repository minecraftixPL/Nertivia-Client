<template>
  <div class="custom-status">
    <textarea
      class="status-textarea"
      @blur="blur"
      @keydown="keyDown"
      ref="textArea"
      v-if="showTextArea"
      placeholder="Click to add status."
    />
    <SimpleMarkdown
      class="status-text"
      @click.native="statusTextClicked"
      v-if="!showTextArea"
      :message="!userStatus ? 'Click to add status.' : userStatus"
    />
  </div>
</template>

<script>
import SimpleMarkdown from "@/components/app/SimpleMarkdown";
import settingsService from '../../services/settingsService';
export default {
  components: {
    SimpleMarkdown,
  },
  data() {
    return {
      showTextArea: false,
      statusText: null,
    };
  },
  mounted() {
    this.statusText = this.userStatus || null;
  },
  methods: {
    statusTextClicked() {
      this.showTextArea = true;
      this.$nextTick(() => {
        this.$refs.textArea.focus();
        if (this.statusText) {
          this.$refs.textArea.value = this.statusText.trim();
        }
      });
    },
    async submit() {
      this.statusText = this.$refs.textArea.value.replace(/\n/g, " ");
      this.showTextArea = false;
      await settingsService.setCustomStatus(this.statusText.trim());
    },
    blur() {
      this.submit();
    },
    keyDown(e) {
      if (e.keyCode === 13) {
        e.preventDefault();
        this.$refs.textArea.blur();
      }
    },
  },
  watch: {
    userStatus(newStatus) {
      this.statusText = newStatus;
    },
  },
  computed: {
    userStatus() {
      return this.$store.getters.user.custom_status;
    },
  },
};
</script>

<style lang="scss" scoped>
.status-textarea {
  color: white;
  background: none;
  border: none;
  outline: none;
  resize: none;
  height: 16px;
  padding: 0;
  margin: 0;
  flex: 1;
  width: initial;
  overflow: hidden;
}
.status-text {
  height: 20px;
  font-size: 13px;
}
.status-text {
  color: rgba(255, 255, 255, 0.8);
  cursor: pointer;
  transition: 0.2s;
  &:hover {
    color: white;
  }
}
</style>

<style>
.status-text {
  display: flex;
  align-items: center;
  align-content: center;
  white-space: pre;
  overflow: hidden;
}
.status-text .emoji {
  height: 13px;
  width: 13px;
}
</style>
