<template>
  <span>
    <scripts-dialog ref="dialog" title="Create Script" width="600" resetOnOpen="true"
      :identifier="identifier" createLabel="Create" cancelLabel="Cancel" @payload="onCommit">
    </scripts-dialog>
  </span>
</template>

<script>
import ScriptsDialog from './ScriptsDialog'
import {
  _createGlobalScript,
  _createTenantScript
} from '../../http/sitewhere-api-wrapper'

export default {

  data: () => ({
  }),

  props: ['identifier', 'tenantToken'],

  components: {
    ScriptsDialog
  },

  methods: {
    // Get handle to nested dialog component.
    getDialogComponent: function () {
      return this.$refs['dialog']
    },

    // Send event to open dialog.
    onOpenDialog: function () {
      this.getDialogComponent().reset()
      this.getDialogComponent().openDialog()
    },

    // Handle payload commit.
    onCommit: function (payload) {
      var component = this
      if (!this.tenantToken) {
        _createGlobalScript(this.$store, this.identifier, payload)
          .then(function (response) {
            component.onCommitted(payload, response)
          }).catch(function (e) {
          })
      } else {
        _createTenantScript(this.$store, this.identifier, this.tenantToken, payload)
          .then(function (response) {
            component.onCommitted(payload, response)
          }).catch(function (e) {
          })
      }
    },

    // Handle successful commit.
    onCommitted: function (payload, response) {
      this.getDialogComponent().closeDialog()
      this.$emit('scriptAdded', payload.id)
    }
  }
}
</script>

<style scoped>
.add-button {
  position: fixed;
  bottom: 16px;
  right: 16px;
}
</style>
