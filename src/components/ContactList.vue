<template>
  <div class="container">
    <h1>Записная книжка</h1>
    <table class="table">
      <thead>
        <th>Id</th>
        <th>Имя</th>
        <th>Телефонный номер</th>
        <th>Электронная почта</th>
        <th>
          <b-button variant="outline-primary" @click="addContact()">Добавить</b-button>
        </th>
      </thead>
      <tbody>
        <tr v-for="(item, index) in contactList" :Key="item.Id">
          <td>{{item.id}}</td>
          <td>{{item.name}}</td>
          <td>{{item.phoneNumber}}</td>
          <td>{{item.email}}</td>
          <td>
            <b-dropdown variant="outline-primary">
              <b-dropdown-item-button @click="editContact(item)">
                <b-icon icon="pencil-square"></b-icon>Редактировать
              </b-dropdown-item-button>
              <b-dropdown-item-button @click="deleteContact(item.id, index)">
                <b-icon icon="x-circle"></b-icon>Удалить
              </b-dropdown-item-button>
            </b-dropdown>
          </td>
        </tr>
      </tbody>
    </table>
    <b-modal id="editModal" @ok="handleOk" @hidden="resetModal">
      <form ref="form" @submit.stop.prevent="handleSubmit">
        <b-form-group
          :state="nameState"
          label="Имя"
          label-for="name-input"
          invalid-feedback="Обязательно для заполнения!"
        >
          <b-form-input id="name-input" v-model="contact.name"></b-form-input>
        </b-form-group>
        <b-form-group
          :state="contactsState"
          label="Номер телефона"
          label-for="phone-number-input"
          invalid-feedback="Введите email или номер телефона!"
        >
          <b-form-input id="phone-number-input" v-model="contact.phoneNumber"></b-form-input>
        </b-form-group>
        <b-form-group
          :state="contactsState"
          label="Электронная почта"
          label-for="email-input"
          invalid-feedback="Введите email или номер телефона!"
        >
          <b-form-input id="email-input" v-model="contact.email"></b-form-input>
        </b-form-group>
      </form>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "ContactList",
  props: {
    apiAddress: String
  },
  data() {
    return {
      edit: false,
      contactList: [],
      contact: { id: 0, name: "", phoneNumber: "", email: "" },
      nameState: null,
      contactsState: null
    };
  },
  mounted() {
    this.getContacts();
  },
  methods: {
    checkFormValidity() {
      this.nameState = !this.contact.name == "";
      this.contactsState = !(
        this.contact.phoneNumber == "" && this.contact.email == ""
      );
      return this.nameState && this.contactsState;
    },
    resetModal() {
      this.nameState = null;
      this.contactsState = null;
      this.contact.id = 0;
      this.contact.name = "";
      this.contact.phoneNumber = "";
      this.contact.email = "";
    },
    handleOk(bvModalEvt) {
      // Prevent modal from closing
      bvModalEvt.preventDefault();
      // Trigger submit handler
      this.handleSubmit();
    },
    handleSubmit() {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return;
      }
      if (this.edit) {
        axios
          .put(this.apiAddress + "/" + this.contact.id, this.contact)
          .then(response => {
            if (response.status === 204) {
              let contact = this.contactList.find(item => {
                return item.id == this.contact.id;
              });
              contact.name = this.contact.name;
              contact.phoneNumber = this.contact.phoneNumber;
              contact.email = this.contact.email;
            }
          });
      } else {
        axios.post(this.apiAddress, this.contact).then(response => {
          if (response.status === 201) {
            this.contactList.push(response.data);
          }
        });
      }
      // Hide the modal manually
      this.$nextTick(() => {
        this.$bvModal.hide("editModal");
      });
    },
    getContacts() {
      axios.get(this.apiAddress).then(response => {
        this.contactList = response.data;
      });
    },
    addContact() {
      this.edit = false;
      this.$bvModal.show("editModal");
    },
    editContact(editContact) {
      this.edit = true;
      this.$bvModal.show("editModal");
      this.contact.id = editContact.id;
      this.contact.name = editContact.name;
      this.contact.phoneNumber = editContact.phoneNumber;
      this.contact.email = editContact.email;
      console.log("editCOntact");
      console.log(this.contact);
    },
    deleteContact(id, index) {
      if (confirm("Вы уверены что хотите удалить этот контакт?")) {
        axios.delete(this.apiAddress + "/" + id).then(response => {
          if (response.status == "200") {
            this.contactList.splice(index, 1);
          }
        });
      }
    }
  }
};
</script>