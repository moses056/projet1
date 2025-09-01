<template>
  <div class="register-page--container" :class="{ 'lecturer-bg': lecturerRole }">
    <div v-if="!$matchMedia.xl" class="login-page--logo">
      <router-link :to="{ name: 'index' }">
        <img src="/images/logo.svg" alt="">
      </router-link>
    </div>
    <div v-else class="desktop-login__left--container">
      <div>
        <img :src="role.img.top" alt="" class="desktop-register__left--img desktop-register__left-top--img">
      </div>
      <div class="flex-row space-between mt-1_5" :class="role.class.midImage">
        <div class="flex-column space-between h100">
          <img :src="role.img.midLeft" alt="" class="desktop-register__left--img desktop-register__left-mid-small--img">
        </div>
        <img :src="role.img.midRight" alt="" class="desktop-register__left--img desktop-register__left-mid-big--img ">
      </div>
      <div>
        <h2 class="desktop-login__left--h2 mt-1_5">
          {{ role.text }}
        </h2>
      </div>
    </div>

    <div class="resgiter-form--container">
      <router-link v-if="$matchMedia.xl" :to="{ name: 'index' }" class="mx-auto mb-1">
        <img class="desktop-nav__logo" src="/images/logo-blue.svg" alt="">
      </router-link>

      <h1 class="login--h1">
        Vous êtes ?
      </h1>
      <div class="login-role--container">
        <div>
          <input id="login-student" v-model="form.role" class="login-radio" type="radio" value="Student" name="role">
          <label class="login-radio--label" for="login-student" @click="chooseStudent">Prestataire</label>
        </div>
        <div>
          <input id="login-lecturer" v-model="form.role" class="login-radio" type="radio" value="Lecturer" name="role">
          <label class="login-radio--label" for="login-lecturer" @click="chooseLecturer">Recruteur</label>
        </div>
      </div>

      <div v-if="!$matchMedia.xl" class="role--choose-effect" :class="{ 'role--student': studentRole, 'role--lecturer': lecturerRole }" />
      <div v-else class="separator mt-1_5 mb-2">
        Inscription
      </div>

      <form @submit.prevent="register" @keydown="form.onKeydown($event)">
        <div class="form__input--group">
          <div class="form-group__container">
            <h4 class="form-group__input-name">
              Prénom
            </h4>
            <div class="">
              <input v-model="form.first_name" :class="{ 'is-invalid': form.errors.has('first_name') }" class="form-group__input-text" type="text" name="first_name" placeholder="ex: Jean" required>
              <has-error :form="form" field="first_name" />
            </div>
          </div>

          <div class="form-group__container">
            <h4 class="form-group__input-name">
              Nom
            </h4>
            <div class="">
              <input v-model="form.last_name" :class="{ 'is-invalid': form.errors.has('last_name') }" class="form-group__input-text" type="text" name="last_name" placeholder="ex: Dupont" required>
              <has-error :form="form" field="last_name" />
            </div>
          </div>
        </div>

        <div class="form-group__container">
          <h4 class="form-group__input-name">
            Email
          </h4>
          <div class="">
            <input v-model="form.email" :class="{ 'is-invalid': form.errors.has('email') }" class="form-group__input-text" type="email" name="email" placeholder="ex: jean.dupont@example.com" autocomplete="username" required>
            <p v-if="form.role === 'Student'" class="form-group__input-info">
              Vous pouvez utiliser des lettres, chiffres et points
            </p>
            <p v-else class="form-group__input-info form-input__info--email">
              <span>Vous pouvez utiliser des lettres, chiffres et points.</span>
              <span>Adresse email professionnelle recommandée.</span>
            </p>
            <has-error :form="form" field="email" />
          </div>
        </div>

        <!-- Password -->
        <div class="form-group__container">
          <h4 class="form-group__input-name">
            Mot de passe
          </h4>
          <div class="">
            <div class="login-input--container">
              <div class="right-tag__group">
                <input v-model="form.password" :class="{ 'is-invalid': form.errors.has('password') }" class="right-tag__input right-tag__input--form" :type="passwordType" name="password" placeholder="ex: Mot2Passe!123" autocomplete="current-password" required>
                <div v-show="hidePassword" class="pointer right-tag" @click="togglePassword">
                  <span class="iconify password__hide-icon" data-icon="carbon:view-off-filled" width="20" height="20" />
                </div>
                <div v-show="!hidePassword" class="pointer right-tag" @click="togglePassword">
                  <span class="iconify password__hide-icon " data-icon="carbon:view-filled" width="20" height="20" />
                </div>
              </div>
              <has-error :form="form" field="password" />
            </div>
            <p class="form-group__input-info">
              Minimum 8 caractères avec lettres, chiffres et symboles
            </p>
            <has-error :form="form" field="password" />
          </div>
        </div>

        <div class="">
          <!-- Submit Button -->
          <v-button :loading="form.busy" class="login-submit--button" :class="{ 'is-lecturer': lecturerRole }">
            S'inscrire
          </v-button>
        </div>
      </form>

      <div class="register-extra">
        <p>
          Vous avez déjà un compte ?
          <router-link :to="{ name: 'login' }" class="login-link">
            <b>Se connecter</b>
          </router-link>
        </p>
      </div>
      <div v-if="$matchMedia.xl" class="desktop-login__footer">
        Yako, Tous droits réservés. &copy; 2024 . | Plateforme d'emploi pour l'Afrique
      </div>
    </div>
  </div>
</template>

<script>
import Form from 'vform'
import { mapGetters } from 'vuex'

export default {
  name: 'RegisterPage',
  layout: 'wide',
  middleware: 'guest',

  metaInfo () { return { title: 'Inscription' } },

  data: () => ({
    studentRole: false,
    lecturerRole: false,
    hidePassword: true,
    passwordType: 'password',

    form: new Form({
      role: 'Student',
      first_name: '',
      last_name: '',
      email: '',
      password: ''
    }),
    mustVerifyEmail: false
  }),

  computed: {
    role () {
      if (this.form.role === 'Student') {
        return {
          img: {
            top: '/images/register-top-student.png',
            midLeft: '/images/register-mid-left-student.png',
            midRight: '/images/login-mid-right-student.png'
          },
          class: {
            midImage: ''
          },
          text: 'Trouvez des opportunités de travail.'
        }
      }

      return {
        img: {
          top: '/images/login-top-lecturer.png',
          midLeft: '/images/login-mid-left-1-lecturer.png',
          midRight: '/images/login-mid-right-lecturer.png'
        },
        class: {
          midImage: 'row-reverse'
        },
        text: 'Publiez des offres d\'emploi.'
      }
    },

    ...mapGetters({
      snackbar: 'notification/snackbar'
    })
  },

  methods: {
    async register () {
      // Register the user.
      const { data } = await this.form.post('/api/register')

      // Must verify email first.
      if (data.status) {
        this.mustVerifyEmail = true
        this.snackbar.open(data.status)
        this.$router.push({ name: 'verification.check', params: { email: this.form.email } })
      } else {
        // Log in the user.
        const { data: { token } } = await this.form.post('/api/login')

        // Save the token.
        this.$store.dispatch('auth/saveToken', { token })

        // Update the user.
        await this.$store.dispatch('auth/updateUser', { user: data })

        // Redirect home.
        this.$router.push({ name: 'index' })
      }
    },
    chooseStudent () {
      this.studentRole = true
      this.lecturerRole = false
      this.form.role = 'Student'
    },
    chooseLecturer () {
      this.lecturerRole = true
      this.studentRole = false
      this.form.role = 'Lecturer'
    },
    togglePassword () {
      this.hidePassword = !this.hidePassword
      this.passwordType = this.hidePassword ? 'password' : 'text'
    }
  }
}
</script>
