
<template>
<v-layout align-star>
<v-flex>
  <v-data-table
  
    :headers="headers"
    :items="categorias"
    :search="search"
    class="elevation-1"
  >
    <template v-slot:item.condicion="{ item }">
      <v-chip :color=>{{ item.condicion }}</v-chip>
    </template>
 
  </v-data-table>
    <template v-slot:top>
      <v-toolbar flat color="white">
        <v-toolbar-title>Categorias</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
            <div class="flex-grow-1"></div>
            <v-spacer></v-spacer>
                        <v-text-field class="text-xs-center" v-model="search" append-icon="search" label="Búsqueda" single-line hide-details></v-text-field>
                        <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on }">
                <v-btn color="primary" dark class="mb-2" v-on="on">Nueva Categoria</v-btn>
              </template>
              <v-card>
                <v-card-title>
                   {{ formTitle }}
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.nombre" label="Nombre"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="8" md="4">
                        <v-text-field v-model="editedItem.descripcion" label="Descripcion"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.condicion" label="Estado"></v-text-field>
                      </v-col>
                     
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <div class="flex-grow-1"></div>
                  <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                  <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
         
          
      </v-toolbar>
    </template>
    <template v-slot:item.action="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        edit
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</v-flex>
</v-layout>
</template>
    <script>
    import axios from 'axios'
      export default {

        data: () => ({
     
              categorias:[],
                 dialog: false,
              headers: [
                
                { text: 'Nombre', value: 'nombre' },
                { text: 'Descripcion', value: 'descripcion', sortable: false },
                 { text: 'Estado', value: 'condicion', sortable: false },
                { text: 'Opciones', value: 'action', sortable: false  }
              ],
               search: '',
              desserts: [],
              editedIndex: -1,
              editedItem: {
                name: '',
                calories: 0,
                fat: 0,
                carbs: 0,
                protein: 0,
              },
              defaultItem: {
                name: '',
                calories: 0,
                fat: 0,
                carbs: 0,
                protein: 0,
                },
               
            }),
        computed: {
          Color(condicion){
            
          },
          formTitle () {
            return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
          },
        },
        watch: {
          dialog (val) {
            val || this.close()
          },
        },
        created () {
        
          this.listar();
        },
        methods: {

         pipeLineCustom (listaCategoria)
         {
           this.categorias = listaCategoria.map(p=> {  return {nombre:p.nombre, descripcion:p.descripcion, condicion: p.condicion? 'activo':'inactivo'} })
         },
          listar(){
            
            axios.get('api/Categorias/Listar').then(response => {
              this.pipeLineCustom(response.data)
            })
            .catch(error => console.log(error))
          },
          editItem (item) {
            this.editedIndex = this.desserts.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialog = true
          },
          deleteItem (item) {
            const index = this.desserts.indexOf(item)
            confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
          },
          close () {
            this.dialog = false
            setTimeout(() => {
              this.editedItem = Object.assign({}, this.defaultItem)
              this.editedIndex = -1
            }, 300)
          },
          save () {
            if (this.editedIndex > -1) {
              Object.assign(this.desserts[this.editedIndex], this.editedItem)
            } else {
              this.desserts.push(this.editedItem)
            }
            this.close()
          },
        },
      }
    </script>