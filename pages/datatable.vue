<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
    :sort-by="[{ key: 'calories', order: 'asc' }]"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>รายชื่อสมาชิก</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">
              เพิ่มข้อมูล
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.id"
                      label="id"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.username"
                      label="username"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.password"
                      label="password"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.email"
                      label="email"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.status"
                      label="status"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.picture"
                      label="picture"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="close">
                Cancel
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="save">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5"
              >Are you sure you want to delete this item?</v-card-title
            >
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeDelete">
                Cancel</v-btn>
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="deleteItemConfirm"
                >OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }" >
      <v-icon class="me-2" size="small" @click="editItem(item)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" @click="deleteItem(item)"> mdi-delete </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize"> Reset </v-btn>
    </template>
  </v-data-table>
</template>

<script>
import axios from "axios"

export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        title: 'id',
        align: 'start',
        sortable: false,
        key: 'id',
      },
      { title: 'username', key: 'username' },
      { title: 'password', key: 'password' },
      { title: 'email', key: 'email' },
      { title: 'status', key: 'status' },
      { title: 'picture', key: 'picture' },
      { title: 'edit/del', key: 'actions', sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      id: '',
      username: '',
      password: '',
      email: '',
      status: '',
      picture: '',
    },
    defaultItem: {
      id: '',
      username: '',
      password: '',
      email: '',
      status: '',
      picture: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'เพิ่มข้อมูล' : 'แก้ไขข้อมูล'
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  async created() {
    this.fetchData()
  },

  methods: {
    initialize() {
      this.desserts = []
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    async save() {
      if (this.editedIndex > -1) {
        try {
          const response = await axios.post("http://localhost:7000/update", this.editedItem)
          const data = response.data
          console.log(data)
          if (data.ok) {
            Object.assign(this.desserts[this.editedIndex], this.editedItem)
            alert('Item updated successfully')
          } else {
            alert(data.error)
          }
        } catch (error) {
          console.error('Error updating item:', error)
          alert(error.message || 'An error occurred while updating the item')
        }
      } else {
        try {
          const response = await axios.post("http://localhost:7000/insert", this.editedItem)
          const data = response.data
          console.log(data)
          if (data.ok) {
            this.desserts.push(this.editedItem)
            alert('Item added successfully')
          } else {
            alert(data.error)
          }
        } catch (error) {
          console.error('Error adding item:', error)
          alert(error.message || 'An error occurred while adding the item')
        }
      }
      this.close()
    },

    async deleteItem(item) {
      if (confirm('Are you sure you want to delete this item?')) {
        try {
          const response = await axios.post("http://localhost:7000/delete", item)
          const data = response.data
          console.log(data)
          if (data.ok) {
            this.desserts = this.desserts.filter(dessert => dessert.id !== item.id)
            await this.fetchData()
          } else {
            alert(data.error)
          }
        } catch (error) {
          console.error('Error deleting item:', error)
        }
      }
    },

    async fetchData() {
      try {
        const response = await axios.get("http://localhost:7000/list")
        this.desserts = response.data.row
      } catch (error) {
        console.error('Error fetching data:', error)
        alert('Failed to refresh data')
      }
    },
  },
}
</script>
