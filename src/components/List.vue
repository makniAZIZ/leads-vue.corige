<template>
  <h1>Liste</h1>
  <ul>
    <li v-for="contact in contacts" :key="contact.id">
      {{ contact.fields.Nom }} {{ contact.fields.Prenom }}
      <button @click="handleUpdate(contact)">Modifier</button>
      <button @click="deleteContact(contact.id)">Supprimer</button>
    </li>
  </ul>

  <label for="nom">Nom</label>
  <input type="text" name="nom" id="nom" v-model="nom" />
  <label for="prenom">Prénom</label>
  <input type="text" name="prenom" id="prenom" v-model="prenom" />
  <label for="email">Email</label>
  <input type="email" name="email" id="email" v-model="email" />
  <button v-if="edit" @click="updateContact(selectedId)">Modifier</button>
  <button v-if="edit" @click="edit = false">Annuler</button>
  <button v-else @click="createContact">Créer un contact</button>
</template>
<script>
const BASE_ID = import.meta.env.VITE_BASE_ID;
const TABLE_NAME = "Leads";
const VIEW_NAME = "Grid view";
const API_TOKEN = import.meta.env.VITE_TOKEN;

export default {
  data() {
    return {
      contacts: [],
      nom: "",
      prenom: "",
      email: "",
      selectedId: null,
      edit: false,
    };
  },
  methods: {
    handleResetForm() {
      this.nom = "";
      this.prenom = "";
      this.email = "";
    },
    getContacts() {
      fetch(
        `https://api.airtable.com/v0/${BASE_ID}/${TABLE_NAME}?view=${VIEW_NAME}`,
        {
          headers: {
            Authorization: `Bearer ${API_TOKEN}`,
          },
        }
      )
        .then((response) => response.json())
        .then((data) => {
          this.contacts = data.records;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    deleteContact(id) {
      fetch(`https://api.airtable.com/v0/${BASE_ID}/${TABLE_NAME}/${id}`, {
        headers: {
          Authorization: `Bearer ${API_TOKEN}`,
        },
        method: "DELETE",
      })
        .then((response) => response.json())
        .then((data) => {
          this.getContacts();
        })
        .catch((error) => {
          console.log(error);
        });
    },
    updateContact(id) {
      fetch(`https://api.airtable.com/v0/${BASE_ID}/${TABLE_NAME}/${id}`, {
        headers: {
          Authorization: `Bearer ${API_TOKEN}`,
          "Content-Type": "application/json",
        },
        method: "PATCH",
        body: JSON.stringify({
          fields: {
            Nom: this.nom,
            Prenom: this.prenom,
            Email: this.email,
          },
        }),
      })
        .then((response) => response.json())
        .then((data) => {
          this.handleResetForm();
          this.getContacts();
          this.edit = false;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    handleUpdate(contact) {
      this.nom = contact.fields.Nom;
      this.prenom = contact.fields.Prenom;
      this.email = contact.fields.Email;
      this.selectedId = contact.id;
      this.edit = true;
    },
    createContact() {
      fetch(`https://api.airtable.com/v0/${BASE_ID}/${TABLE_NAME}`, {
        headers: {
          Authorization: `Bearer ${API_TOKEN}`,
          "Content-Type": "application/json",
        },
        method: "POST",
        body: JSON.stringify({
          fields: {
            Nom: this.nom,
            Prenom: this.prenom,
            Email: this.email,
          },
        }),
      })
        .then((response) => response.json())
        .then((data) => {
          this.handleResetForm();
          this.getContacts();
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },

  mounted() {
    this.getContacts();
  },
};
</script>
<style></style>
