<template>
    <div>
        <div class="edit-wrap mt-3">
            <div class="card">
                <h5 class="card-header">
                    <input type="text" class="form-control" placeholder="請輸入待辦事項" v-model="cacheTodo.message">
                </h5>
                <div class="card-body">
                    <div class="text-left edit-col">
                        <i class="far fa-calendar-alt mr-2 font-awesome-size"></i>
                        <label for="">Deadline</label>
                        <div class="form-inline">
                            <!-- <input type="date" class="form-control border-0" v-model="cacheTodo.startDate"> -->
                            <input type="date" class="form-control border ml-2" v-model="cacheTodo.endDate">
                        </div>
                    </div>
                    <div class="text-left edit-col">
                        <i class="far fa-comment mr-2"></i>
                        <label for="comment">Comment</label>
                        <textarea id="comment" name="" class="form-control w-100" v-model="cacheTodo.comment"></textarea>
                    </div>
                </div>
                <div class="card-footer p-0 border-0">
                    <div class="d-flex">
                        <button class="btn btn-light w-50 edit-btn text-white" @click="closeTodo">取消</button>
                        <button class="btn btn-primary w-50 edit-btn text-white" @click="updateTodo">儲存</button>
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
            cacheTodo: {},
            isNew: false,
        }
    },
    methods: {
        updateTodo () {
            const vm = this;
            const todo = { 
                ...vm.cacheTodo,
                display: true,
                completed: 'progress',
                stared: false,
            };
            if(vm.isNew) {
                const api = 'http://localhost:5000/todos';
                vm.$http.post(api, todo).then((response) => {
                console.log(response.data)
                vm.closeTodo();
                vm.getData();
                });
            }else {
                const api = `http://localhost:5000/todos/${vm.todo.id}`;
                vm.$http.put(api, todo).then((response) => {
                console.log(response)
                vm.closeTodo();
                vm.getData();
        })
            }
        },
        closeTodo () {
            this.$emit('closeEditTodo');
        },
        getData () {
            this.$emit('getNewData');
        }
    },
    created() {
        if(this.todo) {
            this.cacheTodo = { ...this.todo };
        }else {
            this.isNew = true;
        }
    }
}
</script>
