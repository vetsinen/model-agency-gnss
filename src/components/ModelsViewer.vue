<template>
    <div class="models">
        <span class="nav">
            <button v-on:click="prev">prev</button>
            <button v-on:click="next">next</button>
        </span>
        <div>page #{{pageid}} showed.
            people per page <input type="text" v-model.number="pageSize"></div>

        <table>
            <thead>
            <tr>
                <td v-for="(title,key,index) in titles"
                    v-on:click="sort(key)">{{title}}
                    <span>{{sortSymbols[sortOrders[key]]}}</span>
                </td>
            </tr>
            <tr>
                <td v-for="(title,key,index) in titles">
                    <input type="text" size="4" placeholder="filter"
                           v-model="filter[key]"
                           v-on:input="changeHandler(key)"
                           v-on:focus="resetFilter">
                    <button v-on:click="resetFilter">reset</button>
                </td>
            </tr>
            </thead>

            <tr v-for="(employee,index) in paginatedEmployees">
                <td v-for="(value,field,index2) in employee" v-on:click="handleEdit(index,field)">
                    <span v-show="isNotEdited(index,field)">
                        {{value}}</span>
                    <span v-show="!isNotEdited(index,field)">
                        <input type="text"
                               v-on:blur="editDone"
                               v-on:keyup.enter="editDone"
                               v-model="employee[field]">
                    </span>

                </td>
            </tr>
        </table>
    </div>
</template>

<script>
    import data from '@/initialData.js'

    export default {
        name: 'ModelsViewer',
        props: {
            msg: String
        },
        data: function () {
            return {
                titles: data.titles,
                employees: data.items,
                pageSize: 5,
                filter: {},
                edited: {},
                sortOrders: {},
                sortSymbols: {
                    asc: '▼', desc: '▲'
                },
                pageid: this.$route.params.pageid || 0,
                currentPage: function () {
                    return this.employees.splice(this.pageid * this.pageSize)
                }
            }
        },
        methods: {
            getPaginatedEmployees: function () {
                return this.employees.slice(this.pageid * this.pageSize,
                    (this.pageid +1 )* this.pageSize);
            },
            sort: function (key) {
                if (!key in this.sortOrders) {
                    this.sortOrders[key] = "asc";
                }
                else {
                    this.sortOrders[key] = this.sortOrders[key] === 'asc' ? 'desc' : 'asc';
                }
                this.sortRows(key, this.sortOrders[key]);
            },
            changeHandler: function (key) {
                //this.filter = {key:this.filter[key]};
                console.log(key + ' changed to ' + this.filter[key]);
                this.filterRows(key, this.filter[key]);
            },
            handleEdit: function (index, field) {
                if (field === '_id') {
                    return;
                }
                console.log("let's edit " + this.employees[index]._id + ' ' + field);
                this.edited = {_id: this.employees[index]._id, field};
            },
            editDone: function () {
                this.edited = {};
            },
            sortRows: function (field = 'mass', order = 'asc') {
                if (!Object.keys(this.titles).includes(field) ||
                    !['asc', 'desc'].includes(order.toLowerCase())) {
                    console.log('no sorting');
                    return;
                }
                order = order === 'asc' ? 1 : -1;
                this.employees.sort((a, b) => {
                    let diff = a[field] - b[field];
                    if (!isNaN(diff)) {
                        return order * diff;
                    }
                    return order * (a[field] < b[field] ? -1 : 1);
                });
            },
            filterRows: function (field, value) {
                if (!Object.keys(this.titles).includes(field)) {
                    //console.log('no filtration');
                    return;
                }
                this.employees = data.items.filter(row => {
                    return row[field].includes(value)
                })
            },
            resetFilter: function () {
                this.filter = {};
                this.employees = data.items;
                this.pageid=0;
            },
            isNotEdited: function (index, field) {
                return !(this.employees[index]._id === this.edited._id
                    && field === this.edited.field);
            },
            prev: function () {
                if (this.pageid>0){
                    this.pageid--;
                }
            },
            next: function () {
                if (this.pageid<this.lastPage){
                    this.pageid++;}

            }
        },
        watch: {
            '$route'(to) {
                this.pageid = to.params.pageid || 0;
            },
        },
        computed: {
            paginatedEmployees: function () {
                return this.employees.slice(this.pageid * this.pageSize,
                    (this.pageid +1 )* this.pageSize);
            },
            lastPage: function () {
                return Math.ceil(this.employees.length/this.pageSize);
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    table {
        font-family: arial, sans-serif;
        border-collapse: collapse;
        width: 100%;
    }

    thead {
        cursor: pointer;
    }

    td,
    th {
        border: 1px solid #dddddd;
        text-align: left;
        padding: 8px;
    }

    th {
        cursor: pointer;
    }

    tr:nth-child(even) {
        background-color: #dddddd;
    }

</style>
