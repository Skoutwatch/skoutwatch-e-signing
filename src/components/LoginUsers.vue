<template>
  <body
    class="vertical-layout vertical-menu-modern blank-page navbar-floating footer-static"
    data-open="click"
    data-menu="vertical-menu-modern"
    data-col="blank-page"
  >
    <div class="app-content content">
      <div class="content-overlay"></div>
      <div class="header-navbar-shadow"></div>
      <div class="content-wrapper">
        <div class="content-header row"></div>
        <div class="content-body">
          <div class="grid" v-show="token && loader">
            <PreLoader />
          </div>
        </div>
      </div>
    </div>
  </body>
</template>

<script setup>
import PreLoader from "@/components/PreLoader.vue";
import { ref, onMounted } from "vue";
import { useActions, useGetters } from "vuex-composition-helpers/dist";
import { useRouter } from "vue-router";
const route = useRouter();

const { loader } = useGetters({
  loader: "auth/loader",
});

const { dynamicLogin } = useActions({
  dynamicLogin: "auth/dynamicLogin",
  loginUser: "auth/loginUser",
});

const token = ref(null);
onMounted(() => {
  token.value = route.currentRoute.value.query.token;
  dynamicLogin({ dynamic_token: token.value });
});
</script>

<style scoped>
.grid {
  display: grid;
  place-items: center;
  height: 100vh;
}
</style>
