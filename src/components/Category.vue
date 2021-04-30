<template>
  <v-container>
    <v-row>
        <v-col>
            <h1 class="text-center">Categories</h1>
        </v-col>
    </v-row>
    <form @submit.prevent="create()">
        <v-row>
            <v-col cols="6">
                <v-text-field
                    label="name"
                    v-model="name"
                />
            </v-col>
        </v-row>
        <v-row>
            <v-col cols="6">
                <v-btn 
                    type="submit"
                    v-text="'Add'"

                />
            </v-col>
            <v-col cols="6">
                <v-btn 
                    @click="edit()"
                    v-text="'Edit'"
                />
            </v-col>
        </v-row>
    </form>
    <v-data-table
        :headers="headers"
        :items="categories"
    >
        <template v-slot:[`item.action`]="{item}">
            <v-btn 
                @click="deleted(item.id)"
                v-text="'Delete'"
            />
            <v-btn
                @click="edit(item.id)"
                v-text="'Edit'"
            />
        </template>
    </v-data-table>
    
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
    name: 'Category',

    async created() {
        const response = await axios.get('http://localhost:8000/api/categories')
        this.categories = response.data
    },

    data() {
        return {
            name: '',
            categories: [],
            headers: [
                {
                    text: 'ID',
                    value: 'id',
                },
                {
                    text: 'Name',
                    value: 'name',
                },
                {
                    text: 'Action',
                    value: 'action'
                }
            ],

            selectEdit: null
        }
    },
    methods: {
        async create() {
            const response =  await axios.post('http://localhost:8000/api/categories', {name: this.name})
            this.categories.push(response.data)
            this.name = ''
        },

        async deleted(id) {
           if(confirm('Are you sure you want to delete')) {
                await axios.delete('http://localhost:8000/api/categories/' + id,)
                this.categories = this.categories.filter(v => v.id !== id);
           }
        },

        async edit(id = null) {
            if(id) {
                this.selectEdit = id
                this.name =  this.categories.find(v => v.id == this.selectEdit).name
            } else if(this.selectEdit) {
                const response = await axios.put('http://localhost:8000/api/categories/' + this.selectEdit, {name: this.name})
                this.categories.find(v => v.id == this.selectEdit).name = response.data.name
                this.selectEdit = null
                this.name = ''
            } else {
                alert('must select a category')
            }
        }
    }
}
</script>

<style>

</style>