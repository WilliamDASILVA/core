<template>
  <div class="newsletter-form">
    <div class="newsletter-form-container">
      <transition name="fade" mode="out-in">
        <form v-if="!success" key="form" class="newsletter-form-element" @submit.prevent="formSubmitted">
          <div class="newsletter-form-element-input">
            <input
              id="email"
              v-model="email"
              v-validate="'required|email'"
              class="input"
              type="email"
              name="email"
              placeholder="Votre adresse e-mail"
              required
              :disabled="loading"
              :class="{
                'is-invalid': errors.has('email'),
                'is-valid': fields.$scope && fields.$scope.email && fields.$scope.email.valid
              }"
            >
            <span v-if="errors.has('email')" class="caption input-error">
              {{ errors.first('email') }}
            </span>
          </div>
          <div>
            <button
              class="btn btn-blue"
              type="submit"
              :disabled="loading || errors.has('email')"
              :class="{ disabled: loading || errors.has('email') }"
            >
              Envoyer
            </button>
          </div>
        </form>
        <div v-else key="success" class="body-1 newsletter-form-success">
          Merci ! Vous vous êtes bien inscrit à notre newsletter.
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'NewsletterForm',
  data() {
    return {
      email: null,
      loading: false,
      success: false
    }
  },
  methods: {
    formSubmitted() {
      this.loading = true
      this.$nuxt.$loading.start()
      this.$api.post('/newsletters', {
        newsletter: {
          email: this.email
        }
      }).then(() => {
        setTimeout(() => {
          this.$nuxt.$loading.finish()
          this.loading = false
          this.success = true
        }, 3000)
      }).catch(() => {
        this.$nuxt.$loading.finish()
        this.loading = false
        this.success = false
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  @import "@/assets/scss/variables/_colors.scss";

  .newsletter-form{
    &-container{
      width: 40%;
      margin: 0 auto;
      background-color: white;
      padding: 16px;
      margin-top: 32px;
      border-radius: 3px;

      @media only screen and (max-width: 980px) {
        width: 80%;
      }

      @media only screen and (max-width: 783px) {
        width: 100%;
        border-radius: 0;
      }
    }

    &-element{
      display: grid;
      grid-template: 1fr / 3fr 1fr;
      grid-gap: 16px;

      @media only screen and (max-width: 380px) {
        grid-template-columns: 1fr;
      }

      &-label{
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
      }

      &-input{
        display: flex;
        flex-direction: column;
      }
      .btn {
        width: 100%;
      }
    }

    &-success{
      margin: 0;
    }
  }

  .fade{
    &-enter-active, &-leave-active{
      transition: opacity 200ms;
    }

    &-enter, &-leave-to{
      opacity: 0;
    }

    &-enter-to, &-leave{
      opacity: 1;
    }
  }
</style>
