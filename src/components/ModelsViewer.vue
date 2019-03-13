<template>
    <div class="models">
        <div>{{ msg }} for page {{pageid}} </div>

        <table>
            <thead>
            <tr class="flex-grid">
                <td v-for="(title,key,index) in titles" class="col"
                    v-on:click="sort(key)">{{title}}
                </td>
            </tr>
            </thead>

            <tr v-for="(employee,index) in employees">
                <td v-for="field in employee">
                    {{field}}
                </td>
            </tr>

        </table>


    </div>
</template>

<script>
    import ic from '@/initialData.js'

    export default {
        name: 'ModelsViewer',
        props: {
            msg: String
        },
        data: function () {
            return {
                titles: ic.titles,
                employees: ic.items,
                pageSize: 10,
                pageid: this.$route.params.pageid || 1,
                currentPage: function () {
                    return this.employees.splice(this.pageid * this.pageSize)
                }
            }
        },
        methods: {
            sort: function (key) {
                this.sortRows(key);
            },
            sortRows: function (field = 'mass', order = 'asc') {
                if (!Object.keys(this.titles).includes(field) ||
                    !['asc', 'desc'].includes(order.toLowerCase())) {
                    //console.log('no sorting');
                    return;
                }
                order = order === 'asc' ? 1 : -1;
                this.employees.sort((a, b) => {
                    let diff = a[field] - b[field];
                    if (!isNaN(diff)) {
                        return order * diff;
                    }
                    return order * (a[field] < b[field] ? -1 : 0);
                });
            }
        },
        watch: {
            '$route'(to) {
                this.pageid = to.params.pageid || 1;
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
