<template>
  <div class="login-page--container" :class="{ 'lecturer-bg': lecturerRole }">
    <div v-if="!$matchMedia.xl" class="login-page--logo">
      <router-link :to="{ name: 'index' }">
        <img src="/images/logo.svg" alt="">
      </router-link>
    </div>
    <div v-else class="desktop-login__left--container">
      <div>
        <img :src="role.img.top" alt="" class="desktop-login__left--img desktop-login__left-top--img">
      </div>
      <div class="flex-row space-between mt-2_5" :class="role.class.midImage">
        <div class="flex-column space-between h100">
          <img :src="role.img.midLeft" alt="" class="desktop-login__left--img desktop-login__left-mid-small--img">
          <router-link :to="{ name: 'index' }">
            <img src="/images/big-logo.png" class="desktop-login__left-mid-logo--img" alt="">
          </router-link>
        </div>
        <img :src="role.img.midRight" alt="" class="desktop-login__left--img desktop-login__left-mid-big--img ">
      </div>
      <div>
        <h2 class="desktop-login__left--h2 mt-1_5">
          {{ role.text }}
        </h2>
      </div>
    </div>

    <div class="login-form--container">
      <router-link v-if="$matchMedia.xl" :to="{ name: 'index' }" class="mx-auto mb-2_5">
        <img class="desktop-nav__logo" src="/images/logo-blue.svg" alt="">
      </router-link>

      <h1 class="login--h1">
        Qui êtes-vous ?
      </h1>
      <div class="login-role--container">
        <div>
          <input id="login-student" v-model="form.role" class="login-radio" type="radio" value="Student">
          <label class="login-radio--label" for="login-student" @click="chooseStudent">Prestataire</label>
        </div>
        <div>
          <input id="login-lecturer" v-model="form.role" class="login-radio" type="radio" value="Lecturer">
          <label class="login-radio--label" for="login-lecturer" @click="chooseLecturer">Recruteur</label>
        </div>
      </div>

      <div v-if="!$matchMedia.xl" class="role--choose-effect" :class="{ 'role--student': studentRole, 'role--lecturer': lecturerRole }" />
      <div v-else class="separator mt-1_5 mb-2">
        Connexion
      </div>

      <form @submit.prevent="login" @keydown="form.onKeydown($event)">
        <!-- Email -->
        <div class="login-input--container">
          <input v-model="form.email" :class="{ 'is-invalid': form.errors.has('email') }" class="login-input" type="email" name="email" placeholder="Email" autocomplete="username" required>
          <has-error :form="form" field="email" />
        </div>

        <!-- Password -->
        <div class="login-input--container">
          <div class="right-tag__group ">
            <input v-model="form.password" :class="{ 'is-invalid': form.errors.has('password') }" class="right-tag__input login--left-border" :type="passwordType" name="password" placeholder="Mot de passe" autocomplete="current-password" required>
            <div v-show="hidePassword" class="pointer right-tag" @click="togglePassword">
              <span class="iconify password__hide-icon" data-icon="carbon:view-off-filled" width="20" height="20" />
            </div>
            <div v-show="!hidePassword" class="pointer right-tag" @click="togglePassword">
              <span class="iconify password__hide-icon " data-icon="carbon:view-filled" width="20" height="20" />
            </div>
          </div>
          <has-error :form="form" field="password" />
        </div>

        <!-- Remember Me -->
        <div class="login-input--container">
          <label class="checkbox-container checkbox-container--post">Se souvenir de moi
            <input v-model="remember" name="remember" type="checkbox">
            <span class="checkbox-checkmark checkbox-checkmark-remember" />
          </label>
        </div>

        <div class="">
          <!-- Submit Button -->
          <v-button :loading="form.busy" class="login-submit--button" :class="{ 'is-lecturer': lecturerRole }">
            Se connecter
          </v-button>
        </div>
      </form>
      <div class="login-extra">
        <p>
          Vous n'avez pas de compte ?
          <router-link :to="{ name: 'register' }" class="login-link">
            <b>S'inscrire</b>
          </router-link>
        </p>
        <p>
          <router-link :to="{ name: 'password.request' }" class="login-link">
            <b>Mot de passe oublié ?</b>
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
  name: 'LoginPage',
  layout: 'wide',
  middleware: 'guest',

  metaInfo () { return { title: 'Connexion' } },

  data: () => ({
    studentRole: false,
    lecturerRole: false,
    hidePassword: true,
    passwordType: 'password',

    form: new Form({
      role: 'Student',
      email: '',
      password: ''
    }),
    remember: false
  }),

  computed: {
    ...mapGetters({
      snackbar: 'notification/snackbar'
    }),

    role () {
      if (this.form.role === 'Student') {
        return {
          img: {
            top: '/images/login-top-student.png',
            midLeft: '/images/login-mid-left-1-student.png',
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
    }
  },

  methods: {
    async login () {
      try {
        console.log('Login method called with:', {
          role: this.form.role,
          email: this.form.email
        });

        // Désactivation de la validation email pour les lecturers
        // const isLecturer = this.form.email.match(/^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@(?!.*(student)).*.ac.id.*$/)

        // if (this.form.role === 'Lecturer' && !isLecturer) {
        //   this.snackbar.open('You are likely not using a lecturer identity.')
        //   this.chooseStudent()
        //   this.form.role = 'Student'
        //   return
        // } else if (this.form.role === 'Student' && isLecturer) {
        //   this.snackbar.open('You are likely using a lecturer identity. ')
        //   this.chooseLecturer()
        //   this.form.role = 'Lecturer'
        //   return
        // }

        console.log('Attempting login with API...');
        const response = await this.form.post('/api/login');
        console.log('Login response:', response.data);

        this.$store.dispatch('auth/saveToken', {
          token: response.data.token,
          remember: this.remember
        })
        this.$router.back()
      } catch (error) {
        console.error('Login error:', error);
        console.error('Error response:', error.response);
        
        if (error.response && error.response.data) {
          const errorMessage = error.response.data.message || 'Login failed';
          console.log('Error message:', errorMessage);
          
          // Vérifier si c'est le message spécifique
          if (errorMessage.includes('lecturer identity')) {
            console.log('Found lecturer identity error - this should not happen!');
          }
          
          this.snackbar.open(errorMessage);
        } else {
          this.snackbar.open('Une erreur c\'est produite ');
        }
      }
    },

    chooseStudent () {
      this.studentRole = true
      this.lecturerRole = false
    },

    chooseLecturer () {
      this.lecturerRole = true
      this.studentRole = false
    },
    togglePassword () {
      this.hidePassword = !this.hidePassword
      this.passwordType = this.hidePassword ? 'password' : 'text'
    }
  }
}
</script>