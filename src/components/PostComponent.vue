<template>
  <div class="container">
    <h1 class="header">Posts</h1>

    <div class="create-post">
      <div class="input-container">
        <!-- Updated to use a textarea -->
        <textarea
          id="create-post"
          v-model="text"
          placeholder="Write here..."
          class="textarea"
          rows="3"
        ></textarea>
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
  background: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  font-family: "Roboto", sans-serif;
}

/* Header */
.header {
  text-align: center;
  font-size: 32px;
  color: #2c3e50;
  margin-bottom: 20px;
  font-weight: bold;
}

/* Create Post Section */
.create-post {
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.input-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  width: 100%;
}

/* Updated Textarea Styles */
.textarea {
  width: 60%;
  height: auto;
  min-height: 80px;
  padding: 10px;
  border: 1px solid #dcdde1;
  border-radius: 5px;
  font-size: 16px;
  resize: vertical; /* Allows vertical resizing by the user */
  line-height: 1.5;
  overflow: auto;
}

/* Button Styles */
.button {
  padding: 10px 20px;
  background-color: #3498db;
  color: white;
  font-size: 16px;
  font-weight: bold;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}
.button:hover {
  background-color: #2980b9;
}

/* Divider */
.divider {
  border: none;
  height: 1px;
  background: #ecf0f1;
  margin: 20px 0;
}

/* Error Message */
.error {
  border: 1px solid #e74c3c;
  background-color: #f9dcdc;
  color: #c0392b;
  padding: 10px;
  border-radius: 5px;
  font-size: 16px;
  margin-bottom: 20px;
}

/* Posts Container */
.posts-container {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

/* Individual Post */
.post {
  background-color: #ecf0f1;
  border-radius: 8px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  position: relative;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}
.post:hover {
  transform: translateY(-3px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}
.post-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  font-size: 14px;
  color: #7f8c8d;
}
.post-date {
  font-weight: bold;
}
.post-text {
  font-size: 18px;
  font-weight: normal;
  color: #2c3e50;
  margin: 0;
}
</style>
