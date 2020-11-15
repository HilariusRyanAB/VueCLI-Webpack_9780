<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List Tugas</h3>
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn class="mr-2" color="primary" @click="dialogTodo = true">
                    todo selesai
                </v-btn>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>

            <v-data-table :headers="headers" :items="todos" :search="search">
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon class="mr-2" @click="dialog=true, editItem(item)" color="blue">
                        mdi-pencil
                    </v-icon>
                    <v-icon @click="dialogDelete=true, deleteItem(item)" color="red">
                        mdi-delete
                    </v-icon>
                </template>
                <template v-slot:[`item.checkboxes`]="{ item }">
                    <v-checkbox v-model="checkedData" :value="item"></v-checkbox>
                </template>
                <template v-slot:[`item.priority`]="{ item }">
                    <span v-if="item.priority == 'Penting'">
                        <v-chip class="ma-2" color="red" label outlined>
                            {{item.priority}}
                        </v-chip>
                    </span>
                    <span v-else-if="item.priority == 'Tidak penting'">
                        <v-chip class="ma-2" color="green" label outlined>
                            {{item.priority}}
                        </v-chip>
                    </span>
                    <span v-else-if="item.priority == 'Biasa'">
                        <v-chip class="ma-2" color="blue" label outlined>
                            {{item.priority}}
                        </v-chip>
                    </span>
                </template>
            </v-data-table>
        </v-card>

        <span v-if="checkedData.length!=0">
            <v-card align="justify" class="my-5">
                <div class="container">
                    Delete Multiple :
                    <ul v-for="(item,index) in checkedData" :key="index">
                        <li color="black">{{item.task}}</li>
                    </ul>
                    <v-btn color="error" dark class="my-4" @click="deleteSelected">
                        hapus semua
                    </v-btn>
                </div>
            </v-card>
        </span>

        <v-dialog v-model="dialogTodo" persistent max-width="600px">
            <v-card>
                <v-card-title>
                        <span class="headline">Finished Todo</span>
                </v-card-title>
                <v-data-table :headers="selesaiHeader" :items="todosDelete" :search="search">
                    <template v-slot:[`item.priority`]="{ item }">
                    <span v-if="item.priority == 'Penting'">
                        <v-chip class="ma-2" color="red" label outlined>
                            {{item.priority}}
                        </v-chip>
                    </span>
                    <span v-else-if="item.priority == 'Tidak penting'">
                        <v-chip class="ma-2" color="green" label outlined>
                            {{item.priority}}
                        </v-chip>
                    </span>
                    <span v-else-if="item.priority == 'Biasa'">
                        <v-chip class="ma-2" color="blue" label outlined>
                            {{item.priority}}
                        </v-chip>
                    </span>
                </template>
                </v-data-table>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="dialogTodo = false">
                        Close
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>
                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline"><b>Yakin ingin menghapus ?</b></span>
                </v-card-title>
                
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="green darken-1" text @click="dialogDelete=false">
                        Tidak
                    </v-btn>
                    <v-btn color="red darken-1" text @click="deleting">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

    </v-main>
</template>

<script>
    export default {
        name: "List",
        data()
        {
            return {
                search: null,
                i: 0,
                dialog: false,
                dialogTodo: false,
                todosDelete: [],
                checkedData: [],
                dialogDelete: false,
                index:-1,
                status: 0,
                headers: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { text: "Priority", value: "priority" },
                    { text: "Note", value: "note" },
                    { text: "Actions", value: "actions" },
                    {value: "checkboxes"},
                ],
                selesaiHeader: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { text: "Priority", value: "priority" },
                    { text: "Note", value: "note" },
                ],
                todos: [
                    {
                        task: "bernafas",
                        priority: "Penting",
                        note: "huffttt",
                    },
                    {
                        task: "nongkrong",
                        priority: "Tidak penting",
                        note: "bersama tman2",
                    },
                    {
                        task: "masak",
                        priority: "Biasa",
                        note: "masak air 500ml",
                    },
                ],
                formTodo: {
                    task: null,
                    priority: null,
                    note: null,
                },
            };
        },
        methods:
        {
            save()
            {
                if(this.status==0)
                {
                    this.todos.push(this.formTodo);
                }
                else
                {
                    this.todos[this.index].task = this.formTodo.task;
                    this.todos[this.index].priority = this.formTodo.priority;
                    this.todos[this.index].note = this.formTodo.note;
                }
                this.resetForm();
                this.dialog = false;
            },
            cancel()
            {
                this.resetForm();
                this.dialog = false;
            },
            resetForm()
            {
                this.status=0;
                this.formTodo = {
                    task: null,
                    priority: null,
                    note: null,
                };
            },
            editItem(item)
            {
                this.index=this.todos.indexOf(item)
                this.status=1;
                this.formTodo={
                    task: item.task,
                    priority: item.priority,
                    note: item.note
                };
            },
            deleting()
            {
                this.todosDelete.push(this.todos[this.index]),
                this.todos.splice(this.index, 1);
                this.dialogDelete=false;
            },
            deleteItem(item)
            {
                this.index = this.todos.indexOf(item);
            },
            cancelDelete() 
            {
                this.dialogDelete = false;
                this.resetForm();
            },
            deleteSelected()
            {
                for(this.i = 0; this.i < this.checkedData.length; this.i++)
                {
                    this.index = this.todos.indexOf(this.checkedData[this.i]);
                    this.todosDelete.push(this.todos[this.index]);
                    this.todos.splice(this.index, 1);
                }
                this.checkedData = [];
            },
        },
    };
</script>