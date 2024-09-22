<template>
  <f7-page name="home">
    <!-- Top Navbar -->
    <f7-navbar large :sliding="false">
      <f7-nav-left>
        <f7-link icon-ios="f7:menu" icon-md="material:menu" panel-open="left"></f7-link>
      </f7-nav-left>
      <f7-nav-title-large>Framework7 Auth Example</f7-nav-title-large>
    </f7-navbar>
    
    <!-- Page content -->
    <f7-block v-if="user" strong>
      <f7-block-header>You are logged in as {{user.displayName}} {{user.email}}</f7-block-header>
      <f7-button v-on:click="onLogoutClicked">Logout</f7-button>
    </f7-block>

    <f7-block v-else strong>
      <form @submit.prevent="onLoginWithEmailClicked" action="" method="GET">
        <f7-list class="login-list" no-hairlines-md>
          <f7-list-input class="email-input" :value="email" @input="email = $event.target.value" label="Login with your email address" type="email" placeholder="Email address"/>
          <f7-list-input :value="password" @input="password = $event.target.value" label="Provide your password" type="password" placeholder="Password"/>
        </f7-list>
        <f7-button fill type="submit">Login</f7-button>
      </form>
    </f7-block>

    <f7-block v-if="!user" strong>
      <f7-block-title>No account yet?</f7-block-title>
      <f7-button v-if="!showSignupForm" fill v-on:click="showSignupForm = true">Create new account</f7-button>
      <form @submit.prevent="onSignupClicked" action="" method="GET">
        <f7-list v-if="showSignupForm" class="login-list" no-hairlines-md>
          <f7-list-input class="email-input" :value="email_signup" @input="email_signup = $event.target.value" label="Type in your email address" type="email" placeholder="E-mail"/>
          <f7-list-input :value="password_signup" @input="password_signup = $event.target.value" label="Choose a password" type="password" placeholder="Password"/>
        </f7-list>
        <f7-button v-if="showSignupForm" fill type="submit">Sign up</f7-button>
      </form>
    </f7-block>
  </f7-page>
</template>

<script>
import { initializeApp } from 'firebase/app';
import { getAuth, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword } from 'firebase/auth';

export default {
  data() {
    return {
      user: null,
      email: '',
      password: '',
      email_signup: '',
      password_signup: '',
      showSignupForm: false,
    };
  },
  methods: {
  onLoginWithEmailClicked() {
    const auth = getAuth(); // Mendapatkan instance Auth
    signInWithEmailAndPassword(auth, this.email, this.password)
      .then((userCredential) => {
        // Berhasil login
        const user = userCredential.user;
        console.log('Login successful:', user);
        if (this.$f7 && this.$f7.dialog) {
          this.$f7.dialog.alert('Login successful!', 'Success'); // Dialog login sukses
        } else {
          alert('Login successful!'); // Fallback untuk dialog
        }
        this.user = user; // Set user ke state
      })
      .catch((error) => {
        // Gagal login
        console.error('Login failed:', error);
        if (this.$f7 && this.$f7.dialog) {
          this.$f7.dialog.alert('Login failed: ' + error.message + '. Please try again.', 'Error');
        } else {
          alert('Login failed: ' + error.message); // Fallback untuk dialog
        }
      });
  },

  onSignupClicked(e) {
    e.preventDefault();

    const auth = getAuth(); // Mendapatkan instance Auth
    createUserWithEmailAndPassword(auth, this.email_signup, this.password_signup)
      .then((userCredential) => {
        // User berhasil dibuat
        console.log('User created successfully', userCredential);
        if (this.$f7 && this.$f7.dialog) {
          this.$f7.dialog.alert('User created successfully!', 'Success'); // Dialog success
        } else {
          alert('User created successfully!'); // Fallback untuk dialog
        }
      })
      .catch((error) => {
        // Gagal membuat user
        console.error('Failed to create user', error);
        if (this.$f7 && this.$f7.dialog) {
          this.$f7.dialog.alert('Failed to create user: ' + error.message + '. Please try again.', 'Error');
        } else {
          alert('Failed to create user: ' + error.message); // Fallback untuk dialog
        }
      });
  },

  onLogoutClicked() {
    const auth = getAuth(); // Mendapatkan instance Firebase Authentication
    auth.signOut()
      .then(() => {
        // Berhasil logout
        this.user = null; // Reset user setelah logout
        if (this.$f7 && this.$f7.dialog) {
          this.$f7.dialog.alert('You have successfully logged out.', 'Success'); // Dialog success
        } else {
          alert('You have successfully logged out.'); // Fallback untuk dialog
        }
      })
      .catch((error) => {
        // Gagal logout
        console.error('Logout failed:', error);
        if (this.$f7 && this.$f7.dialog) {
          this.$f7.dialog.alert('Logout failed. Please try again.', 'Error'); // Dialog error
        } else {
          alert('Logout failed. Please try again.'); // Fallback untuk dialog
        }
      });
  }
},

  mounted() {
    const firebaseConfig = {
      apiKey: "AIzaSyARGcSAibEQNpdAyhFOryiy_nf_BM4dlMI",
      authDomain: "login-2927d.web.app",
    };

    // Inisialisasi Firebase
    const app = initializeApp(firebaseConfig);
    const auth = getAuth(app);

    // Ketika status autentikasi pengguna berubah
    onAuthStateChanged(auth, (user) => {
      if (user) {
        console.log('User is logged in:', user);
      } else {
        console.log('No user is logged in.');
      }
      this.user = user;
    });
  },
};
</script>
