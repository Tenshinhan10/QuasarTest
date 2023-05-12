<template>
    <div class="q-pa-md">
        <div class="q-py-md">
          <q-btn icon="add" @click="basic = true"></q-btn>
          <q-dialog v-model="basic" transition-show="rotate" transition-hide="rotate">
            <q-card>
              <q-card-section>
                <div class="text-h6" style="width:500px;text-align:center">Create Account.</div>
              </q-card-section>
              <q-card-section class="q-pt-none">
                <center>
                  <div class="q-pa-md" style="max-width: 400px">
                    <q-form @submit="onSubmit" class="q-gutter-md">
                      <q-input outlined v-model="createfname" label="Firstname" />
                      <q-input outlined v-model="createlname" label="Lastname" />
                      <q-input outlined v-model="createusername" label="Username" />
                      <q-input
                        outlined
                        v-model="createpassword"
                        label="Password"
                        type="password"
                      />
                      <q-input outlined v-model="createemail" label="Email" />
                      <q-input outlined v-model="createimage" label="URL Image" />
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
        </div>
        <q-table title="Data" :rows="rows" :columns="columns" row-key="name">
          <template v-slot:body-cell-image="props">
            <q-td :props="props">
              <q-img :src="props.row.image" class="userimg"></q-img>
            </q-td>
          </template>
          <template v-slot:body-cell-actions="props">
            <q-td :props="props">
              <!-- <q-btn icon="mode_edit" @click="onEdit(props.row.id)"></q-btn> -->
              <q-btn icon="mode_edit" @click="onEdit(props.row.id)"></q-btn>
              <q-dialog v-model="basicupdate" transition-show="rotate" transition-hide="rotate">
                <q-card>
                  <q-card-section>
                    <div class="text-h6" style="width:500px;text-align:center">Edit Account.</div>
                  </q-card-section>
                  <q-card-section class="q-pt-none">
                    <center>
                      <div class="q-pa-md" style="max-width: 400px">
                        <q-form @submit="onSubmitUpdate" class="q-gutter-md">
                            <q-input outlined v-model="updateid" label="ID" readonly />
                            <q-input outlined v-model="updatefname" label="Firstname" />
                            <q-input outlined v-model="updatelname" label="Lastname" />
                            <q-input outlined v-model="updateusername" label="Username" />
                            <q-input outlined v-model="updatepassword" label="Password" type="password" />
                            <q-input outlined v-model="updateemail" label="Email" />
                            <q-input outlined v-model="updateimage" label="URL Image" />
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
              <q-btn icon="delete" @click="onDelete(props.row.id)"></q-btn>
            </q-td>
          </template>
        </q-table>
      </div>
</template>

<script setup>
import { defineComponent, ref} from "vue";
import { Notify } from "quasar";

const columns = ref([
  { name: "id", align: "left", label: "ID", field: "id", sortable: true },
  {
    name: "fname",
    align: "left",
    label: "fname",
    field: "fname",
    sortable: true,
  },
  {
    name: "lname",
    align: "left",
    label: "lname",
    field: "lname",
    sortable: true,
  },
  {
    name: "email",
    align: "left",
    label: "email",
    field: "email",
    sortable: true,
  },
  {
    name: "username",
    align: "left",
    label: "username",
    field: "username",
    sortable: true,
  },
  { name: "image", align: "center", label: "image", field: "image" },
  {
    name: "actions",
    align: "center",
    label: "actions",
    field: "id"
  },
]);

const rows = ref([]);
const fetchData = () => {
  fetch("http://localhost:5000/data")
    .then((res) => res.json())
    .then((result) => {
      console.log("User data:", result);
      rows.value = result;
    })
    .catch((error) => {
      console.log("Error fetching user data:", error);
    });
};
fetchData();

const basicupdate = ref(false);
const updateid = ref("");
const updatefname = ref("");
const updatelname = ref("");
const updateusername = ref("");
const updatepassword = ref("");
const updateemail = ref("");
const updateimage = ref("");
const onEdit = (id) => {
  basicupdate.value = true;
  updateid.value = id;
  fetchDataUpdate();
};

const fetchDataUpdate = () => {
  fetch(`http://localhost:5000/data/${updateid.value}`)
    .then((res) => res.json())
    .then((result) => {
      console.log("User data:", result);
      updatefname.value = result.fname;
      updatelname.value = result.lname;
      updateusername.value = result.username;
      updatepassword.value = result.password;
      updateemail.value = result.email;
      updateimage.value = result.image;
    })
    .catch((error) => {
      console.log("Error fetching user data:", error);
    });
};
fetchDataUpdate();

const onSubmitUpdate = () => {
  const myHeaders = new Headers();
  myHeaders.append("Content-Type", "application/json");

  const raw = JSON.stringify({
    fname: updatefname.value,
    lname: updatelname.value,
    username: updateusername.value,
    password: updatepassword.value,
    email: updateemail.value,
    image: updateimage.value,
  });

  const requestOptions = {
    method: "PUT",
    headers: myHeaders,
    body: raw,
    redirect: "follow",
  };

  fetch(`http://localhost:5000/data/${updateid.value}`, requestOptions)
    .then((response) => response.json())
    .then((result) => {
      Notify.create({
            message: "Successful data update.",
            position: "top",
            color: "positive",
          });
      fetchData();
    })
    .catch((error) => console.log("error", error));
};

const onDelete = (id) => {
  var myHeaders = new Headers();
  myHeaders.append("Content-Type", "application/json");

  var requestOptions = {
    method: 'DELETE',
    headers: myHeaders,
    body: JSON.stringify({ id }),
    redirect: 'follow'
  };

  fetch(`http://localhost:5000/data/${id}`, requestOptions)
    .then(response => {
      if (response.ok) {
        Notify.create({
            message: "Delete successfully.",
            position: "top",
            color: "positive",
          });
        fetchData();
      }
      throw new Error('Network response was not ok.');
    })
    .then(result => {
      fetchData();
    })
    .catch(error => console.log('error', error));
};

const basic = ref(false);
const createfname = ref("");
const createlname = ref("");
const createusername = ref("");
const createpassword = ref("");
const createemail = ref("");
const createimage = ref("");
const onSubmit = () =>{
    var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "fname": createfname.value,
  "lname": createlname.value,
  "username": createusername.value,
  "password": createpassword.value,
  "email": createemail.value,
  "image": createimage.value
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("http://localhost:5000/data", requestOptions)
  .then(response => response.json())
  .then(result => {
    Notify.create({
            message: "Create successfully.",
            position: "top",
            color: "positive",
          });
    fetchData();
  })
  .catch(error => console.log('error', error));
}

</script>


<style>
.userimg{
  width: 50%;
  aspect-ratio: 10/9;
  object-fit:contain;
  border-radius: 50%;
}
</style>
