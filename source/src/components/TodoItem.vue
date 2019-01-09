<template>
    <div>
        <div class="todo-wrap mt-3">
            <div class="todo-item p-4 pl-5 pr-5 border" :class="{'stared': todo.stared, 'bg-white': !todo.stared}"  v-if="todo.display === true">
                <div class="text-muted handle">
                    <i class="fas fa-ellipsis-v"></i> 
                </div>
                <div class="text-muted delete-item" @click="deleteData">
                    <i class="fas fa-times"></i>    
                </div>      
                <div class="d-flex">
                    <div class="d-flex align-items-center">
                        <a href="#" class="mr-3" @click.prevent="updateCompleted">
                            <i class="far fa-square fa-2x" v-if="todo.completed === 'progress'"></i>
                            <i class="far fa-check-square fa-2x text-primary" v-if="todo.completed === 'completed'"></i>
                        </a>
                    </div>
                    <div>
                        <div class="h3 text-dark" :class="{completed: todo.completed === 'completed'}">{{ todo.message }}</div>
                        <div class="text-muted">
                            <i class="far fa-calendar-alt mr-1"></i>
                            <span class="mr-4">{{ todo.endDate }}</span>
                            <i class="far fa-comment"></i>
                            <span class="ml-1">{{ todo.comment }}</span>
                        </div>
                    </div>
                    <div class="ml-auto d-flex align-items-center">
                        <a href="#" @click.prevent="updateStar">
                            <i class="far fa-star fa-2x mr-3 text-primary" v-if="!todo.stared"></i>
                            <i class="fas fa-star fa-2x mr-3 text-primary" v-else></i>
                        </a>
                        <a href="#"><i class="fas fa-pencil-alt fa-2x" @click.prevent="editTodo(todo.id)"></i></a>  
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['todo'],
    data () {
        return {
        }
    },
    methods: {
        updateStar () {
            const vm = this;
            const api = `http://localhost:5000/todos/${vm.todo.id}`;
            const todo = {
                ...vm.todo
            };
            console.log(todo)
            todo.stared = !vm.todo.stared;
            vm.$http.put(api, todo).then((response) => {
                console.log(response.data)
                vm.getData();
            });
        },
        updateCompleted () {
            const vm = this;
            const api = `http://localhost:5000/todos/${vm.todo.id}`;
            const todo = {
                ...vm.todo
            };
            console.log(todo)
            if (todo.completed === 'progress') {
                todo.completed = 'completed';
            }else {
                todo.completed = 'progress';
            }
            vm.$http.put(api, todo).then((response) => {
                console.log(response.data)
                vm.getData();
            });
        },
        deleteData () {
            const vm = this;
            const api = `http://localhost:5000/todos/${vm.todo.id}`;
            const todo = {
                ...vm.todo
            };
            todo.display = false;
            vm.$http.put(api, todo).then((response) => {
                console.log(response.data)
                vm.getData();
            });
            // const vm = this;
            // const api = `http://localhost:5000/todos/${vm.todo.id}`;
            // const sortApi = `http://localhost:5000/sort/${vm.todo.id}`;
            // vm.$http.delete(api).then((response) => {
            //     console.log(response)
            //     vm.getData();
            // });
            // vm.$http.delete(sortApi).then((response) => {
            //     console.log(response)
            // });
        },
        editTodo (id) {
            this.$emit('editCurrentTodo', id)
        },
        getData () {
            this.$emit('getNewData');
        }
    }
}
</script>