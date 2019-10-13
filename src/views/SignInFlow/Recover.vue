<template>
  <div
    class="container"
    :class="{'light-background' : !isDarkMode, 'dark-background' : isDarkMode}"
  >
    <RequestAccount />
    <div class="login">
      <img
        src="@/assets/DCHQ.svg"
        v-show="isDarkMode"
      >
      <img
        src="@/assets/DCHQ-dark.svg"
        v-show="!isDarkMode"
      >
      <h4 :class="{'light-text' : isDarkMode, 'dark-text' : !isDarkMode}">Recover Account</h4>
      <form @submit.prevent="onSubmit">
        <input
          type="email"
          placeholder="Email"
          :class="{'light-field' : isDarkMode, 'dark-field' : !isDarkMode}"
          v-model="email"
          required
        >
        <button>Send Email</button>
      </form>
      <router-link
        to="/signIn"
        :class="{'light-link': isDarkMode, 'dark-link' : !isDarkMode}"
      >Already have an account? Sign in now.</router-link>
      <ThemeSwitch />
    </div>
  </div>
</template>

<script>
import RequestAccount from "@/components/RequestAccount";
import ThemeSwitch from "@/components/ThemeSwitch";
import { auth } from "@/main";

export default {
  name: "Recover",
  components: {
    RequestAccount,
    ThemeSwitch
  },
  data() {
    return {
      email: null,
      password: null,
      hasText: false,
      text: ""
    };
  },
  computed: {
    isDarkMode() {
      return this.$store.getters.isDarkMode;
    }
  },
  methods: {
    onSubmit() {
      const email = this.email;

      auth
        .requestPasswordRecovery(email)
        .then(() => {
          this.$router.push({
            name: "signIn",
            params: {
              userRecoveredAccount: true,
              email: email
            }
          });
        })
        .cath(error => {
          alert("Error: " + error);
        });
    }
  },
  mounted() {
    const params = this.$route.params;
    if (params.userLoggedOut) {
      this.hasText = true;
      this.text = "You have logged out!";
    }
  }
};
</script>

<style lang="scss" scoped>
.dark-field {
  background: rgba(198, 208, 235, 0.2);
  border: 1px solid rgba(0, 0, 0, 0.2);
  &::placeholder {
    color: rgba(0, 0, 0, 0.3);
  }
}

.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
.login {
  width: 400px;
}
</style>