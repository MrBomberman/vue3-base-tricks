<template>
        <form class="form" @submit.prevent>
            <h4>Post creation</h4>
            <my-input 
                v-model="post.title"
                type="text" 
                placeholder="Name"
            />
            <my-input
                v-model="post.body"
                type="text" 
                placeholder="Description"
            />
            <my-button
                style='align-self: flex-end; margin-top: 10px'
                @click="createPost"
            >Create</my-button>
        </form>
</template>

<script>
export default {
    data() {
        return {
            post: {
                title: '',
                body: ''
            }
        }
    },
    methods: {
        createPost(){
            this.post.id = Date.now();
            this.$emit('create', this.post)
            this.post = {
                title: '',
                body: ''
            }
        }
    },
    // watch: {
    //     post: {
    //         handler(newVal) {
    //             console.log(newVal)
    //         },
    //         deep: true // глубокое отслеживание
    //     }
    // },
}
</script>

<style scoped>
    .form {
        display: flex;
        flex-direction: column;
    }
</style>