<template>
  <div class="q-pa-md">
    <q-form @submit="createvisit()" ref="formVisits" class="q-gutter-md">
      <div class="row q-gutter-md">
        <div class="col-12 col-md-5 col-sm-12">
          <label class="text-weight-bold text-h4" for="">Visitor</label>

          <q-input
            label="Visitor code"
            v-model.trim="form.visitor.code"
            :rules="[formRulesMixin_requiredInput]"
            lazy-rules
            debounce="500"
            :loading="loading.visitor"
            @input="requestVisitorByCode()"
          />
          <q-input
            label="Full name"
            v-model.trim="form.visitor.fullName"
            :rules="[formRulesMixin_requiredInput]"
          />
          <q-input
            label="Title / Position"
            v-model.trim="form.visitor.titlePosition"
            :rules="[formRulesMixin_requiredInput]"
          />
          <q-input
            v-model.trim="form.reasonVisit"
            label="Reason for visit"
            filled
            type="textarea"
            :rules="[formRulesMixin_requiredInput]"
          />
        </div>
        <div class="col-12 col-md-5 col-sm-12">
          <label class="text-weight-bold text-h4" for="">Receiver</label>

          <q-input
            label="Receiver code"
            v-model.trim="form.receiver.code"
            :rules="[formRulesMixin_requiredInput]"
            debounce="500"
            :loading="loading.receiver"
            @input="requestReceiverByCode()"
          />
          <q-input
            label="Full name"
            v-model.trim="form.receiver.fullName"
            :rules="[formRulesMixin_requiredInput]"
          />
          <q-input
            label="Title / Position"
            v-model.trim="form.receiver.titlePosition"
            :rules="[formRulesMixin_requiredInput]"
          />
        </div>
      </div>
      <div class="row q-mt-lg">
        <q-btn color="primary" icon="save" label="SAVE" type="submit" />
      </div>
    </q-form>
  </div>
</template>

<script>
import formMixin from 'src/mixins/FormRules'

export default {
  name: 'PageIndex',
  mixins: [formMixin],
  data () {
    return {
      form: {
        visitor: {
          code: null,
          fullName: null,
          titlePosition: null
        },
        receiver: {
          code: '',
          fullName: '',
          titlePosition: ''
        },
        reasonVisit: null
      },
      loading: {
        visitor: false,
        receiver: false
      }
    }
  },
  methods: {
    createvisit () {
      this.$store
        .dispatch('visits/create', this.form)
        .then(response => {
          this.form.visitor.code = null
          this.form.visitor.fullName = null
          this.form.visitor.titlePosition = null
          this.form.reasonVisit = null
          this.$q.notify({
            type: 'positive',
            message: 'Visit register.'
          })
        })
        .catch(error => {
          console.log('createvisit -> error', error)
          this.$q.notify({
            type: 'negative',
            message: 'Internal server error, try again...'
          })
        })
    },
    requestVisitorByCode () {
      this.loading.visitor = true
      this.$store
        .dispatch('users/getUsers', `?code=${this.form.visitor.code}`)
        .then(response => {
          if (response.data.data.length > 0) {
            const visitor = response.data.data[0]
            this.form.visitor.fullName = visitor.fullName
            this.form.visitor.titlePosition = visitor.titlePosition
            this.$q.notify({
              type: 'positive',
              message: 'Visitor existis.'
            })
          } else {
            this.$q.notify({
              type: 'negative',
              message: 'Visitor not existis.'
            })
          }
        })
        .catch(error => {
          console.log(error)
        })
        .finally(() => {
          this.loading.visitor = false
        })
    },
    requestReceiverByCode () {
      this.loading.receiver = true
      this.$store
        .dispatch('users/getUsers', `?code=${this.form.receiver.code}`)
        .then(response => {
          if (response.data.data.length > 0) {
            const receiver = response.data.data[0]
            this.form.receiver.fullName = receiver.fullName
            this.form.receiver.titlePosition = receiver.titlePosition
            this.$q.notify({
              type: 'positive',
              message: 'Receiver existis.'
            })
          } else {
            this.$q.notify({
              type: 'negative',
              message: 'Receiver not existis.'
            })
          }
        })
        .catch(error => {
          console.log(error)
        })
        .finally(() => {
          this.loading.receiver = false
        })
    }
  }
}
</script>
