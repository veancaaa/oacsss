<template>
<div>
  <NavBar />
  <div
    id="app"
    class="small-container"
  >
    <h1 class="table-title">Admin add account</h1>

    
    <div v-if="showModal">
      <Modal  @close="toggleModal">
        <admin-form @add:admin="addadmin" />
      </Modal>
    </div>
    <button class="Modal" @click="toggleModal">{{ showModal === false ?  'Add New Admin' : 'Close' }}</button>
    <h3>Total of {{ admins.length }} Admin Account</h3>
    <admin-table
      :admins="admins"
      @delete:admin="deleteadmin"
      @activate:admin="ActivateAdmin"
      @edit:admin="editadmin"
    />
  </div>
</div>
</template>

<script>
import NavBar from './Navbar.vue';
import adminTable from '../../components/Admin/AdminTable.vue'
import adminForm from '../../components/Admin/AdminForm.vue'
import Modal from '../../components/Modal.vue';
import Parse from "parse";

export default {
  name: "app",
  components: {
    NavBar,
    adminTable,
    adminForm,
  },
  data() {
    return {
      showModal : false,
      admins: []
    }
  },

  mounted() {
    this.getadmins()
  },

  methods: {
    toggleModal() {
      this.showModal = !this.showModal
    },
    async getadmins() {
      try {
          const Data = Parse.Object.extend("_User");
          const query = new Parse.Query(Data);
          query.find({useMasterKey: true}).then(
            async (data) => {
              var res = await data.map((e) => e.attributes);
              console.log(res.filter((e) => e.ACL.permissionsById['role:admin']))
              var employee = res.map((e) => e.ACL.permissionsById['role:Employee'])
              console.log(data.map((e) => e.attributes));
              this.admins = res.filter((e) => e.ACL.permissionsById['role:admin'])
        //await store.dispatch('allAppointment' , res )
      })
      } catch (error) {
        console.error(error)
      }
    },

    async addadmin(admin) {
      try {
       console.log(JSON.parse(JSON.stringify(admin)));
        var res = JSON.parse(JSON.stringify(admin));
        //console.log(res.username)
        var groupACL  = new Parse.ACL();
        groupACL.setRoleWriteAccess("admin" , true);
        groupACL.setRoleReadAccess("admin" , true);
        groupACL.setPublicReadAccess(true);
        var user = new Parse.User();
        user.set("username", res.username);
        user.set("email", res.email);
        user.set("password", res.password);
        user.setACL(groupACL);
        user.signUp().then(function success(user) {
          console.log("Signed Up" , user);
        } , function error(err) {
          console.error(err)
        })
        location.reload();
        this.admins = [...this.admins, data]
      } catch (error) {
        console.error(error)
      }
    },

    async editadmin(id, updatedadmin) {
      console.log(updatedadmin)
      // try {
      //   const response = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
      //     method: 'PUT',
      //     body: JSON.stringify(updatedadmin),
      //     headers: { "Content-type": "application/json; charset=UTF-8" }
      //   })
      //   const data = await response.json()
      //   this.admins = this.admins.map(admin => admin.id === id ? data : admin)
      // } catch (error) {
      //   console.error(error)
      // }
    },

    async deleteadmin(admin) {
      console.log(admin)
      try {
          const User = Parse.Object.extend("_User");
          const query = new Parse.Query(User);
          query.equalTo('email', admin.email);
          query.first().then(
          async (data) => {
          console.log(data.attributes);
           data.set("status", "InActive");
           try {
            data.save(null, { useMasterKey: true });
             location.reload();
            return "User updated successfully!";
          } catch (e) {
            return e.message;
          }
        },
        (error) => {
          console.log(error);
        }
      );
      } catch (error) {
        console.error(error)
      }
    },
    async ActivateAdmin(admin) {
      console.log(admin)
      try {
          const User = Parse.Object.extend("_User");
          const query = new Parse.Query(User);
          query.equalTo('email', admin.email);
          query.first().then(
          async (data) => {
          console.log(data.attributes);
           data.set("status", "Active");
           try {
            data.save(null, { useMasterKey: true });
            location.reload();
            return "User updated successfully!";
          } catch (e) {
            return e.message;
          }
        },
        (error) => {
          console.log(error);
        }
      );
      } catch (error) {
        console.error(error)
      }
    },
  },
}
</script>

<style scoped src="../../assets/admin.css">
</style>
