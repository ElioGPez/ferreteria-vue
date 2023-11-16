<template>
  <div>
    <v-overlay :value="isLoading">
      <v-progress-circular
        indeterminate
        :size="56"
        :width="8"
      ></v-progress-circular>
    </v-overlay>
    <v-container fluid>
      <v-data-table
        :headers="headers"
        :items="items"
        sort-by="calories"
        class="elevation-1"
      >
        <template v-slot:item.price="{ item }"> ${{ item.price }} </template>
        <template v-slot:item.imageName="{ item }">
          <v-img
            lazy-src="https://picsum.photos/id/11/10/6"
            max-height="57"
            max-width="117"
            :src="`http://localhost:8090/img/${item.imageName}`"
          ></v-img>
        </template>
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>ARTICULOs</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="1024px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  + NUEVO
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="text-h5">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          v-model="editedArticle.name"
                          label="Nombre"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-file-input
                          v-model="image"
                          accept="image/*"
                          label="Imágen"
                        ></v-file-input>
                      </v-col>

                      <v-col cols="12" sm="6" md="12">
                        <v-text-field
                          v-model="editedArticle.description"
                          label="Descripción"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-autocomplete
                          :items="categoriesList"
                          :filter="customFilter"
                          v-model="categorySelected"
                          item-value="id"
                          @change="getSubCategories(categorySelected)"
                          color="white"
                          item-text="name"
                          label="Categoría"
                        ></v-autocomplete>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-autocomplete
                          :items="subCategoriesList"
                          :filter="customFilter"
                          color="white"
                          item-text="name"
                          label="Subcategoría"
                        ></v-autocomplete>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          type="number"
                          v-model="editedArticle.stock"
                          label="Stock"
                          :rules="[naturalNumberRule]"
                          inputmode="numeric"
                          pattern="[0-9]*"
                          min="0"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          type="number"
                          min="0"
                          v-model="editedArticle.price"
                          label="Precio"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          type="number"
                          min="0"
                          v-model="editedArticle.cost"
                          label="Costo"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close">
                    Cancelar
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="save">
                    Guardar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-dialog v-model="dialogDelete" max-width="500px">
              <v-card>
                <v-card-title class="text-h5"
                  >Deseas eliminar el artículo?</v-card-title
                >
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeDelete"
                    >Cancelar</v-btn
                  >
                  <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                    >OK</v-btn
                  >
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:item.actions="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize"> Reset </v-btn>
        </template>
      </v-data-table>
    </v-container>
  </div>
</template>

<script>
export default {
  name: "App",

  data: () => ({
    isLoading: false,
    categorySelected: null,
    categoriesList: [],
    subCategoriesList: [],
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        text: "#",
        align: "start",
        sortable: false,
        value: "id",
      },
      { text: "NOMBRE", value: "name" },
      { text: "DESCRIPCION", value: "description" },
      { text: "STOCK", value: "stock" },
      { text: "COSTO", value: "cost" },
      { text: "PRECIO", value: "price" },
      { text: "Imagen", value: "imageName", sortable: false },
      { text: "Actions", value: "actions", sortable: false },
    ],
    desserts: [],
    items: [],
    editedIndex: -1,
    editedArticle: {
      name: "",
      description: "",
      stock: 0,
      price: 0,
      cost: 0,
      imageName: "",
    },
    defaultArticle: {
      name: "",
      description: "",
      stock: 0,
      price: 0,
      cost: 0,
      imageName: "",
    },
    image: null,
  }),
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo Artículo" : "Editar Artículo";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.initialize();
  },
  mounted() {
    this.getArticles();
    this.getCategories();
  },
  methods: {
    naturalNumberRule(value) {
      const naturalNumberRegex = /^[1-9]\d*$/; // Expresión regular para números naturales
      if (naturalNumberRegex.test(value)) {
        to;
        return true; // La entrada es un número natural
      } else {
        return "Ingrese un número natural válido"; // Mensaje de error si la entrada no es un número natural
      }
    },
    customFilter(item, queryText, itemText) {
      const textOne = item.name.toLowerCase();
      const textTwo = item.id.toString();
      const searchText = queryText.toLowerCase();

      return (
        textOne.indexOf(searchText) > -1 || textTwo.indexOf(searchText) > -1
      );
    },
    getSubCategories(categoryId) {
      if (!categoryId) return;
      this.isLoading = true;

      const api = "http://localhost:8090/api/sub-categories/allByCategory";
      this.axios
        .get(api, {
          params: {
            category_id: categoryId,
          },
        })
        .then((response) => {
          this.subCategoriesList = response.data;
          this.isLoading = false;
        });
    },
    getCategories() {
      this.isLoading = true;

      const api = "http://localhost:8090/api/categories";
      this.axios.get(api).then((response) => {
        this.categoriesList = response.data;
        this.isLoading = false;
      });
    },
    getArticles() {
      this.isLoading = true;

      const api = "http://localhost:8090/api/articles";
      this.axios.get(api).then((response) => {
        this.items = response.data;
        this.isLoading = false;
      });
    },
    initialize() {
      this.desserts = [
        {
          name: "Frozen Yogurt",
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
        },
        {
          name: "Ice cream sandwich",
          calories: 237,
          fat: 9.0,
          carbs: 37,
          protein: 4.3,
        },
        {
          name: "Eclair",
          calories: 262,
          fat: 16.0,
          carbs: 23,
          protein: 6.0,
        },
        {
          name: "Cupcake",
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
        },
        {
          name: "Gingerbread",
          calories: 356,
          fat: 16.0,
          carbs: 49,
          protein: 3.9,
        },
        {
          name: "Jelly bean",
          calories: 375,
          fat: 0.0,
          carbs: 94,
          protein: 0.0,
        },
        {
          name: "Lollipop",
          calories: 392,
          fat: 0.2,
          carbs: 98,
          protein: 0,
        },
        {
          name: "Honeycomb",
          calories: 408,
          fat: 3.2,
          carbs: 87,
          protein: 6.5,
        },
        {
          name: "Donut",
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 4.9,
        },
        {
          name: "KitKat",
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
        },
      ];
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.desserts.splice(this.editedIndex, 1);
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      /*if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem);
      } else {
        this.desserts.push(this.editedItem);
      }*/
      const formData = new FormData();
      formData.append("name", this.editedArticle.name);
      formData.append("description", this.editedArticle.description);
      formData.append("stock", this.editedArticle.stock);
      formData.append("price", this.editedArticle.price);
      formData.append("cost", this.editedArticle.cost);
      formData.append("subcategory_id", 1);
      formData.append("location_id", 1);
      formData.append("unit_id", 1);
      formData.append("image", this.image);

      const api = "http://localhost:8090/api/articles/save";
      this.axios
        .post(api, formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((response) => {
          console.log(response.data);
          this.items = response.data;
        });
      //  this.close();
    },
  },
};
</script>