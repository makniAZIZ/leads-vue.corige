<template>
  <h1>Liste</h1>
  <ul>
    <li v-for="contact in contacts" :key="contact.id">
      <a :href="contact.fields.url">
        {{ contact.fields.titre }}
        <div class="image"><img :src="contact.fields.image" alt="" /></div>
      </a>
      <button @click="handleUpdate(contact)">Modifier</button>
      <button @click="deleteContact(contact.id)">Supprimer</button>
    </li>
  </ul>

  <label for="url">url</label>
  <input type="url" name="url" id="url" v-model="url" />
  <button v-if="edit" @click="updateContact(selectedId)">Modifier</button>
  <button v-if="edit" @click="edit = false">Annuler</button>
  <button v-else @click="createContact">Créer un contact</button>
</template>
<script>
const BASE_ID = import.meta.env.VITE_BASE_ID;
const TABLE_NAME = "url";
const VIEW_NAME = "Grid view";
const API_TOKEN = import.meta.env.VITE_TOKEN;

export default {
  data() {
    return {
      url: "",
      selectedId: null,
      edit: false,
      contacts: [],
    };
  },
  methods: {
    handleResetForm() {
      this.url = "";
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
            url: this.url,
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
      this.url = contact.fields.url;
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
            url: this.url,
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
<style>
.image {
  width: 100px; /* Remplacez par la largeur souhaitée en pixels */
  height: 100px; /* Remplacez par la hauteur souhaitée en pixels */
}

.image img {
  width: 100%; /* Pour remplir la boîte parente */
  height: 100%; /* Pour remplir la boîte parente */
  border-radius: 8px; /* Ajoute un arrondi aux coins de l'image */
}
</style>
