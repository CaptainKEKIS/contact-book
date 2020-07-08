<template>
  <div>
    <h1>Записная книжка</h1>
    <table>
      <thead>
        <th>Id</th>
        <th>Имя</th>
        <th>Телефонный номер</th>
        <th>Электронная почта</th>
      </thead>
      <tbody>
        <tr v-for="(contact, index) in contactList" :Key="contact.Id">
          <td>{{contact.id}}</td>
          <td>{{contact.name}}</td>
          <td>{{contact.phoneNumber}}</td>
          <td>{{contact.email}}</td>
          <td>
            <button @click="editContact(contact.Id, index)">Редактировать</button>
          </td>
          <td>
            <button @click="deleteContact(contact.Id, index)">Удалить</button>
          </td>
        </tr>
      </tbody>
    </table>
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
      contactList: []
    };
  },
  mounted() {
    this.getContacts();
  },
  methods: {
    getContacts() {
      axios.get(this.apiAddress).then(response => {
        this.contactList = response.data;
      });
    },
    deleteContact(id, index) {
      if (confirm("Вы уверены что хотите удалить этот контакт?")) {
        axios.delete();
      }
    }
  }
};
</script>