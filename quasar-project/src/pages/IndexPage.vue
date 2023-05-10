<template>
  <center>
    <h3>Login Form</h3>
    <div class="q-pa-md" style="max-width: 400px">
      <form class="q-gutter-md" @submit.prevent="submitForm">
        <q-input outlined v-model="usernamelog" label="Username" type="text" />
        <q-input
          outlined
          v-model="passwordlog"
          label="Password"
          type="password"
        />
        <div>
          Do you have an account?
          <q-btn
            flat
            label="Register"
            color="primary"
            @click="basic = true"
          /><br />
        </div>
        <q-btn label="Login" type="submit" color="primary" />
      </form>
    </div>
    <q-dialog v-model="basic" transition-show="rotate" transition-hide="rotate">
      <q-card>
        <q-card-section>
          <div class="text-h6">Apply for an account. Fill out information.</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <center>
            <div class="q-pa-md" style="max-width: 400px">
              <q-form @submit="onSubmit" class="q-gutter-md">
                <q-input outlined v-model="fname" label="Firstname" />
                <q-input outlined v-model="lname" label="Lastname" />
                <q-input outlined v-model="username" label="Username" />
                <q-input
                  outlined
                  v-model="password"
                  label="Password"
                  type="password"
                />
                <q-input outlined v-model="email" label="Email" />
                <q-input outlined v-model="image" label="URL Image" />
                <q-btn
                  v-close-popup
                  label="Submit"
                  type="submit"
                  color="primary"
                />
                <q-btn label="Cancel" color="red" v-close-popup />
              </q-form>
            </div>
          </center>
        </q-card-section>
      </q-card>
    </q-dialog>
  </center>
</template>

<script>
import { defineComponent, ref } from "vue";
import { Notify } from "quasar";
import { routes } from "../router/routes";

export default defineComponent({
  name: "IndexPage",
  setup() {
    const usernamelog = ref("");
    const passwordlog = ref("");

    const fname = ref("");
    const lname = ref("");
    const username = ref("");
    const password = ref("");
    const email = ref("");
    const image = ref("");

    const onSubmit = () => {//register
      var myHeaders = new Headers();
      myHeaders.append("Content-Type", "application/json");

      var raw = JSON.stringify({
        fname: fname.value,
        lname: lname.value,
        username: username.value,
        password: password.value,
        email: email.value,
        image: image.value,
      });

      var requestOptions = {
        method: "POST",
        headers: myHeaders,
        body: raw,
        redirect: "follow",
      };

      fetch("http://localhost:5000/data", requestOptions)
        .then((response) => response.json())
        .then((result) => {
          routes.push("/");
        })
        .catch((error) => console.log("error", error));
    };

    const submitForm = () => { //login
      if (!usernamelog.value || !passwordlog.value) {
        Notify.create({
          message: "Please enter both username and password.",
          position: "top",
          color: "warning"
        });

        return;
      }
      const requestBody = {
        username: usernamelog.value,
        password: passwordlog.value,
      };
      fetch("http://localhost:5000/login", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(requestBody),
      })
        .then((response) => {
          if (response.status === 200) {
            Notify.create({
              message: "Login successfully.",
              position: "top",
              color: "positive"
            });
            routes.push('/home');
          } else {
            Notify.create({
              message: "Login failed. Please try again.",
              position: "top",
              color: "negative",
              icon: "warning",
            });
          }
        })
        .catch((error) => console.log("error", error));
    };

    return {
      basic: ref(false),
      fixed: ref(false),
      fname,
      lname,
      username,
      password,
      email,
      image,
      usernamelog,
      passwordlog,
      onSubmit,
      submitForm,
    };
  },
});
</script>
