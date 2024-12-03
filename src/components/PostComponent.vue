<template>
  <div class="container">
    <h1 class="header">Posts</h1>

    <div class="create-post">
      <div class="input-container">
        <input
          type="text"
          id="create-post"
          v-model="text"
          placeholder="Write something amazing..."
          class="input"
        />
        <button class="button" v-on:click="createPost">Post</button>
      </div>
    </div>

    <hr class="divider" />

    <p class="error" v-if="error">{{ error }}</p>

    <div class="posts-container">
      <div
        class="post"
        v-for="(post, index) in posts"
        v-bind:key="post._id"
        v-on:dblclick="deletePost(post._id)"
      >
        <div class="post-header">
          <span class="post-date">{{
            `${post.createdAt.getDate()}/${post.createdAt.getMonth()}/${post.createdAt.getFullYear()}`
          }}</span>
          <span class="post-index">Post #{{ index + 1 }}</span>
        </div>
        <p class="post-text">{{ post.text }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import PostService from "../PostService";

export default {
  name: "PostComponent",
  data() {
    return {
      posts: [],
      error: "",
      text: "",
    };
  },
  async created() {
    try {
      this.posts = await PostService.getPosts();
    } catch (err) {
      this.error = err.message;
    }
  },
  methods: {
    async createPost() {
      await PostService.insertPost(this.text);
      this.posts = await PostService.getPosts();
    },
    async deletePost(id) {
      await PostService.deletePost(id);
      this.posts = await PostService.getPosts();
    },
  },
};
</script>

<style scoped>
/* General Container */
.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  background: #f3f8ff;
  border-radius: 12px;
  box-shadow: 0 6px 10px rgba(0, 0, 0, 0.15);
  font-family: 'Roboto', sans-serif;
  text-align: center;
}

/* Header */
.header {
  font-size: 36px;
  color: #2c3e50;
  margin-bottom: 20px;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
}

/* Create Post Section */
.create-post {
  margin-bottom: 30px;
}

.input-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  gap: 15px;
}

.input {
  width: 70%; /* Narrower width */
  height: 50px; /* Taller input box */
  padding: 10px;
  font-size: 18px;
  border: 2px solid #3498db;
  border-radius: 8px;
  outline: none;
  box-shadow: 0 4px 6px rgba(52, 152, 219, 0.2);
  transition: all 0.3s ease;
}

.input:focus {
  border-color: #2ecc71;
  box-shadow: 0 4px 8px rgba(46, 204, 113, 0.5);
}

.button {
  width: 150px;
  height: 50px;
  background: linear-gradient(45deg, #6a11cb, #2575fc);
  color: #fff;
  font-size: 18px;
  font-weight: bold;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.button:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 12px rgba(106, 17, 203, 0.3);
}

/* Divider */
.divider {
  border: none;
  height: 2px;
  background: #ecf0f1;
  margin: 30px 0;
}

/* Error Message */
.error {
  border: 1px solid #e74c3c;
  background-color: #f9dcdc;
  color: #c0392b;
  padding: 15px;
  border-radius: 8px;
  font-size: 16px;
  margin-bottom: 20px;
  text-align: left;
}

/* Posts Container */
.posts-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* Individual Post */
.post {
  background-color: #ffffff;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  position: relative;
  transition: transform 0.2s, box-shadow 0.2s;
  cursor: pointer;
}

.post:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.post-header {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
  color: #7f8c8d;
  margin-bottom: 10px;
}

.post-date {
  font-weight: bold;
  color: #34495e;
}

.post-index {
  color: #95a5a6;
}

.post-text {
  font-size: 20px;
  font-weight: normal;
  color: #2c3e50;
  margin: 0;
}
</style>
