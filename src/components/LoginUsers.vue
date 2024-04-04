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
          <div v-show="!token && !loader" class="auth-wrapper auth-basic px-2">
            <div class="auth-inner">
              <div class="card mb-0">
                <div class="card-body">
                  <!-- <LoginWidthSolana /> -->

                  <Form
                    class="auth-login-form mt-2"
                    @submit="onSubmit"
                    :validation-schema="schema"
                    v-slot="{ errors }"
                  >
                    <div class="mb-1">
                      <label>Email</label>
                      <Field
                        name="email"
                        type="text"
                        class="form-control"
                        :class="{ 'is-invalid': errors.email }"
                      />
                      <div class="invalid-feedback">{{ errors.email }}</div>
                    </div>

                    <div class="mb-1">
                      <label>Passworded</label>
                      <Field
                        name="password"
                        type="password"
                        class="form-control"
                        :class="{ 'is-invalid': errors.password }"
                      />
                      <div class="invalid-feedback">{{ errors.password }}</div>
                    </div>

                    <div class="form-button">
                      <button
                        id="submit"
                        type="submit"
                        class="btn btn-primary w-100 waves-effect waves-float waves-light"
                        :disabled="loading"
                      >
                        <span
                          v-show="loading"
                          class="spinner-border spinner-border-sm"
                        ></span>
                        <span>Proceed</span>
                      </button>
                    </div>
                  </Form>
                </div>
              </div>
            </div>
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
import { Form, Field } from "vee-validate";
import * as Yup from "yup";
// import LoginWidthSolana from "./LoginWidthSolana.vue";
import { useRouter } from "vue-router";
const route = useRouter();

const { loader } = useGetters({
  loader: "auth/loader",
});

const { dynamicLogin, loginUser } = useActions({
  dynamicLogin: "auth/dynamicLogin",
  loginUser: "auth/loginUser",
});

const schema = Yup.object().shape({
  email: Yup.string().required("Email is required").email("Email is invalid"),
  password: Yup.string()
    .min(6, "Password must be at least 6 characters")
    .required("Password is required"),
});

const loading = ref(false);

const onSubmit = (params) => {
  loading.value = true;
  params.entry_point = "User";

  loginUser(params);
  loading.value = false;
};

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
