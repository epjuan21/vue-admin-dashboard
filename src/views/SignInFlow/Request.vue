<template>
  <div
    class="container"
    :class="{'light-background' : !isDarkMode, 'dark-background' : isDarkMode}"
  >
    <div class="login">
      <img
        src="@/assets/DCHQ.svg"
        v-show="isDarkMode"
      >
      <img
        src="@/assets/DCHQ-dark.svg"
        v-show="!isDarkMode"
      >
      <h4 :class="{'light-text' : isDarkMode, 'dark-text' : !isDarkMode}">Request Account</h4>
      <form @submit.prevent="onSubmit">
      <input
        type="email"
        placeholder="Email"
        :class="{'light-field' : isDarkMode, 'dark-field' : !isDarkMode}"
        v-model="email"
        required
      >
      <button>Request Account</button>
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
import ThemeSwitch from "@/components/ThemeSwitch";

export default {
  name: "Request",
  components: {
    ThemeSwitch
  },
  data() {
      return {
          email: null,
          password: null,
          hasText: false,
          text: ""
      }
  },
  computed: {
    isDarkMode() {
      return this.$store.getters.isDarkMode;
    }
  },
  methods: {
    onSubmit() {
        const email = this.email;

        // Slack API logic
        let slackURL = new URL("https://slack.com/api/chat.postMessage");
     
        const data = {
            token: 'xoxp-727032751877-728888292583-793518258277-7995057f54705cab672ab7bc8a2c164c',
            channel: "hq",
            text: `${email} has requested admin access to HQ. Please go to Netlify to invite them.`
        }

        slackURL.search = new URLSearchParams(data);

        fetch(slackURL).then(() => {
            this.$router.push({
                name: "signIn",
                params: {
                    userRequestedAccount: true,
                    email: email
                }
            })
        }).catch(error => {
            alert("Error " + error);
        });


    },
  },
  mounted () {
      const params = this.$route.params;
      if (params.userLoggedOut){
          this.hasText = true;
          this.text = "You have logged out!";
      }
  },
}
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