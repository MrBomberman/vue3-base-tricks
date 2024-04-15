<template>
    <h1>Page with posts</h1>
    <my-input v-model="searchQuery" placeholder="Search..."/>
    <div class="app__btns">
        <my-button @click="showDialog">Create post</my-button>
    <my-select v-model="selectedSort" :options="sortOptions"></my-select>
    </div>
    <my-dialog v-model:show='dialogVisible'>
            <post-form 
            @create="createPost"    
        />
    </my-dialog>
        <post-list 
            :posts="sortedAndSearchedPosts"
            @remove="removePost"
            v-if="!isPostLoading"
        />
        <div v-else>Loading...</div>
        <div ref="observer" class='observer'></div>

        <!-- <div class="page__wrapper">
            <div v-for="pageIndex in totalPage" :key="pageIndex"
            class="page"
            :class="{
               'currentPage' : page === pageIndex
            }"
            @click="changePage(pageIndex)"
            >
            {{ pageIndex }}
            </div>
        </div> -->
</template>

<script>
import PostForm from '@/components/PostForm.vue';
import PostList from '@/components/PostList.vue';
import axios from 'axios';
import MyButton from '@/components/UI/MyButton.vue';
import MySelect from '@/components/UI/MySelect.vue';
export default {
    components: {
        PostForm, PostList,
        MyButton, MySelect
    },
    data() {
        return {
        posts: [
            
        ],
            dialogVisible: false,
            isPostLoading: false,
            selectedSort: '',
            sortOptions: [
                {value: 'title', name: 'By name'},
                {value: 'body', name: 'By description'}
            ],
            searchQuery: "",
            page: 1,
            limit: 10,
            totalPage: 0
        } 
    },
    methods: {
        createPost(post) {
            this.posts.push(post)  
            this.dialogVisible = false
        },
        removePost(post){
            this.posts = this.posts.filter((p) => p.id !== post.id)
        },
        showDialog(){
            this.dialogVisible = true
        },
        async fetchPosts(){
            try {
                this.isPostLoading = true
                setTimeout(async () => {
                    const response = await axios.get(`https://jsonplaceholder.typicode.com/posts`, {
                        params: {
                            _page: this.page,
                            _limit: this.limit
                        }
                    });
                    this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit)
                    this.posts = response.data
                    this.isPostLoading = false
                }, 500)
            } catch(err){
                console.log(err)
            }
        },
        async loadMorePosts(){
            try {
                this.page = this.page + 1
                const response = await axios.get(`https://jsonplaceholder.typicode.com/posts`, {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }
                });
                this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit)
                this.posts = [...this.posts, ...response.data]
            } catch(err){
                console.log(err)
            }
        }
        // changePage(pageNumber){
        //     this.page = pageNumber
        // },
    },
    mounted() {
        this.fetchPosts();
        this.$refs.observer
        let options = {
            rootMargin: "0px",
            threshold: 1.0,
        }

        const callback = (entries, observer) => {
            if(entries[0].isIntersecting && this.page < this.totalPage){
                this.loadMorePosts()
            }
        }

        let observer = new IntersectionObserver(callback, options);
        observer.observe(this.$refs.observer)
    },
    computed: {
        sortedPosts() {
            return [...this.posts.sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))];
        },
        sortedAndSearchedPosts() {
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        },
    },
    watch: {
        // page() {
        //     this.fetchPosts()
        // },
        // selectedSort(newValue) {
        //     this.posts.sort((post1, post2) => {
        //         return post1[newValue]?.localeCompare(post2[newValue])
        //     })
        // },
    },
}
</script>

<style>
    .app__btns {
        display: flex;
        justify-content: space-between;
        margin: 15px 0;
    }
    .page__wrapper {
        display: flex;
        margin-top: 15px;
    }
    .page {
        border: 1px solid black;
        padding: 10px
    }
    .currentPage {
        border: 2px solid teal;
    }
    .observer {
        height: 20px;
        background: green;
    }
</style>