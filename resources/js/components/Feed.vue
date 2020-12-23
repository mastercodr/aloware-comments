<template>
    <div>
        <div class="panel panel-default" v-for="post in posts">
            <div class="panel-body">
                <p>
                    {{ post.content }}
                </p>
            </div>
            <div class="panel-footer">
                <div class="col-md-12 pt-2">
                    <div><h4>Comments</h4></div>
                    <comment-list v-if="post.comments" :collection="post.comments"
                                  :comments="post.comments.root"></comment-list>
                </div>

                <div class="clearfix"></div>

                <div class="col-md-12 pt-3 mt-3 list-group-item">
                    <div><h4>Leave a comment</h4></div>

                    <div class="form-group row">
                        <label for="author" class="col-1 col-form-label">Name</label>
                        <div class="col-3">
                            <input id="author" name="author" placeholder="Your name" v-model="post.author" type="text"
                                   required="required" class="form-control">
                        </div>
                    </div>
                    <div class="form-group row">
                        <label for="reply" class="col-1 col-form-label">Comment</label>
                        <div class="col-11">
                            <input id="reply" name="reply" placeholder="Your comment" type="text" v-model="post.reply"
                                   v-on:keyup.enter="comment(post)" required="required" class="form-control">
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import CommentList from './CommentList.vue';

export default {
    data() {
        return {
            post: {
                reply: '',
                author: ''
            }
        }
    },
    computed: {
        posts() {
            return this.$store.state.posts;
        }
    },
    components: {
        'comment-list': CommentList
    },
    mounted() {
        this.getPosts();
    },
    methods: {
        getPosts() {
            axios.get('/api/post').then(response => {
                if (!response.data.error) {
                    response.data.data.forEach((post) => {
                        this.$store.commit('pushPost', post);
                    });
                }
            });
        },
        comment(post) {
            axios.post('/api/comment', {content: post.reply, author: post.author, post_id: post.id}).then(response => {
                if (!response.data.error) {
                    post.reply = '';
                    let payLoad = {
                        post_id: post.id,
                        comments: response.data.data
                    };
                    this.$store.commit('updateComments', payLoad);
                }
            });
        }
    }
}
</script>
