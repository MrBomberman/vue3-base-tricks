<template>
    <div class='post'>
        <div>
            <div>{{post.id}}</div>
            <div><strong>Name: </strong>{{post.title}}</div>
            <div><strong>Body: </strong>{{post.body}}</div>
        </div>
        <div class="post__buttons">
            <my-button @click="$router.push(`/posts/${post.id}`)">Open</my-button>
            <my-button
                @click="$emit('remove', post)"
            >Delete</my-button>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        post: {
            type: Object,
            required: true
        }
    }
}
</script>

<style scoped>
    .post {
        padding: 15px;
        border: 2px solid green;
        margin-top: 15px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .post__buttons {
        display: flex
    }
</style>