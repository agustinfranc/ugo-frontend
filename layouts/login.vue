<template>
  <v-app>
    <!-- Logo -->
    <v-container>
      <v-row>
        <v-col cols="2">
          <v-img src="https://picsum.photos/510/300?random" width="64" height="64"></v-img>
        </v-col>
        <v-col cols="10">
          <h2 class="d-inline">{{appName}}</h2>
        </v-col>
      </v-row>
    </v-container>

    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>

    <Footer />
  </v-app>
</template>

<script>
import Footer from "@/components/Footer";
import { auth } from "@/plugins/firebase.js";

export default {
  components: {
    Footer
  },
  data() {
    return {
      appName: process.env.APP_NAME
    };
  },
  methods: {
    async observer() {
      return new Promise((resolve, reject) => {
        auth.onAuthStateChanged(user => {
          if (user) {
            console.log("User is signed in");
            location.href = "/";
            resolve(user);
          }
        });
      });
    }
  },
  async mounted() {
    let store = this.$store;
    let self = this;
    if (auth.currentUser) {
      await store.dispatch("saveUser", {
        uid: user.uid,
        displayName: user.displayName,
        photoURL: user.photoURL,
        email: user.email,
        emailVerified: user.emailVerified
      });
      console.log("currentUser", auth.currentUser);
    } else {
      let user = await self.observer();
      if (!user) location.href = "/login";
      await store.dispatch("saveUser", {
        uid: user.uid,
        displayName: user.displayName,
        photoURL: user.photoURL,
        email: user.email,
        emailVerified: user.emailVerified
      });
      console.log("observer", user);
    }
  }
};
</script>