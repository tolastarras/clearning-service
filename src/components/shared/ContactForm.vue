<template lang="html">
  <div class="contact-form">
    <alert-message v-show="alert.show" :alert="alert"></alert-message>
    <v-container pa-0 ma-0>
      <h1 class="display-1 header white--text font-weight-bold" v-text="formTitle"></h1>
      <v-form v-model="isValidData" ref="form">
        <v-layout row wrap>
          <v-flex xs12 sm6>
            <v-text-field class="pr-4"
              label="First Name"
              v-model="firstName"
              :rules="nameRules"
              required
            ></v-text-field>
          </v-flex>
          <v-flex xs12 sm6>
            <v-text-field
              label="Last Name"
              v-model="lastName"
              :rules="nameRules"
              required
            ></v-text-field>
          </v-flex>
          <v-flex xs12 sm6>
            <v-text-field class="pr-4"
              label="E-mail"
              v-model="email"
              :rules="emailRules"
              required
            ></v-text-field>
          </v-flex>
          <v-flex xs12 sm6>
            <v-text-field
              label="Phone"
              v-model="phone"
              hint="For example, 3055551212"
              :rules="phoneRules"
              required
            ></v-text-field>
          </v-flex>
          <v-flex xs12 sm6 v-if="quote || this.$route.params.quote">
            <v-select class="pr-4"
              label="Service"
              v-model="service"
              :items="serviceTypes"
              :rules="serviceRules"
              required
            ></v-select>
          </v-flex>
          <v-flex xs12 sm6 v-if="quote || this.$route.params.quote">
            <v-select
              label="Frequency"
              v-model="frequency"
              :items="frequencies"
              :rules="frequencyRules"
              required
            ></v-select>
          </v-flex>
        </v-layout>
        <v-textarea
          label="Message"
          v-model="message"
          :rules="messageRules"
          :counter="maxMessageChars"
          required
        ></v-textarea>
        <v-btn
          class="primary"
          :loading="loading"
          :disabled="loading || !isValidData"
          @click.prevent="onSubmit"
        >Send
          <span slot="loader" class="custom-loader">
            <v-icon light>cached</v-icon>
          </span>
        </v-btn>
      </v-form>
    </v-container>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
import EmailService from '@/services/EmailService'
import AlertMessage from './Alert'
import rules from '@/rules/validationRules'

export default {
  props: ['quote'],
  components: {
    AlertMessage
  },
  data: () => ({
    formTitle: '',
    loader: null,
    loading: false,
    isValidData: false,
    alert: {
      show: false,
      type: '',
      text: ''
    },
    firstName: '',
    lastName: '',
    email: '',
    phone: '',
    service: '',
    frequency: '',
    message: '',
    maxMessageChars: 300,

    // validation rules
    nameRules: rules.name(),
    emailRules: rules.email(),
    phoneRules: rules.phone(),
    serviceRules: rules.service(),
    frequencyRules: rules.frequency(),
    messageRules: rules.message()
  }),
  created () {
    this.onLoad()
  },
  methods: {
    closeAlert () {
      setTimeout(() => (this.alert.show = false), 5000)
    },
    onLoad () {
      // params from services page to auto-fill free quote form
      let params = this.$route.params

      this.service = params.service
      this.message = params.message
      this.formTitle = this.quote || params.quote ? 'Get a Free Quote' : 'Send us a Message'
    },
    onSubmit () {
      this.loading = true

      EmailService.postFormData('message', {
        firstName: this.firstName,
        lastName: this.lastName,
        email: this.email,
        phone: this.phone,
        message: this.message,
        service: this.service,
        frequency: this.frequency,
        type: this.quote ? 'Free Quote' : 'Comment'
      })
        .then(({ data }) => {
          this.alert = {
            show: true,
            type: data.type,
            text: data.message
          }
          this.$refs.form.reset()
          this.loading = false
          this.closeAlert()
        })
        .catch(() => {
          this.loading = false
          this.alert = {
            show: true,
            type: 'error',
            text: 'Unable to Send Message'
          }
        })
    }
  },
  computed: {
    ...mapState(['frequencies']),
    ...mapGetters(['serviceTypes'])
  }
}
</script>

<style lang="scss" scoped>
form {
  padding: 2em;
  background: $color-02;
}

h1 {
  margin: 0;
  width: 100% !important;
}

.header {
  background-color: lighten($color-10, 3);
  padding: .3em 0 .5em 0 !important;
}

.custom-loader {
  animation: loader 1s infinite;
  display: flex;
}

.container {
  padding: 0 !important;
  border: 12px solid lighten($color-10, 3);
  border-radius: 4px;
}

@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }

  to {
    transform: rotate(360deg);
  }
}

@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }

  to {
    transform: rotate(360deg);
  }
}

@-o-keyframes loader {
  from {
    transform: rotate(0);
  }

  to {
    transform: rotate(360deg);
  }
}

@keyframes loader {
  from {
    transform: rotate(0);
  }

  to {
    transform: rotate(360deg);
  }
}

@media (max-width: 960px) {
  .contact-form .container {
    border-width: 2px;

  }
  .v-input {
    font-size: $font-32;
  }
}

@media (max-width: 425px) {
  .container {
    border: 0;
    border-radius: 0;

    form {
      padding: 2em 0 0;

      .v-input {
        padding-right: 0 !important;
      }
    }
  }
}
</style>
