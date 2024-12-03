<template>
  <div class="background">
    <div class="container">
      <h1 class="header">Posts ✏️</h1>

      <div class="create-post">
        <div class="input-container">
          <!-- Centered Textarea -->
          <textarea
            id="create-post"
            v-model="text"
            placeholder="Write here..."
            class="textarea"
            rows="5"
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
            <span class="post-date">
              {{
                new Date(post.createdAt).toLocaleDateString('en-US', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </span>
            <span class="post-index"><b>Post {{ index + 1 }}</b></span>
          </div>
          <p class="post-text">{{ post.text }}</p>
        </div>
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
/* Background */
.background {
  background-color: #f3f4f6; /* Light gray background */
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 80px; /* 4x larger padding */
}

/* Container */
.container {
  max-width: 3600px; /* 4x larger container width */
  margin: 0 auto;
  padding: 200px; /* 4x larger padding */
  background: #ffffff; /* White background for the container */
  border-radius: 60px; /* 4x larger border radius */
  box-shadow: 0 32px 80px rgba(0, 0, 0, 0.2); /* 4x larger shadow */
  font-family: "Roboto", sans-serif;
}

/* Header */
.header {
  text-align: center;
  font-size: 1.5rem; /* Keep font size the same (24px) */
  color: #2c3e50;
  margin-bottom: 80px; /* 4x larger margin */
  font-weight: bold;
}

/* Create Post Section */
.create-post {
  margin-bottom: 80px; /* 4x larger margin */
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.input-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 40px; /* 4x larger gap */
  width: 100%;
}

/* Textarea */
.textarea {
  width: 80%; /* 4x larger width */
  height: auto;
  min-height: 480px; /* 4x larger height */
  padding: 40px; /* 4x larger padding */
  border: 4px solid #dcdde1; /* 4x larger border thickness */
  border-radius: 20px; /* 4x larger border radius */
  font-size: 1rem; /* Keep font size the same (16px) */
  resize: vertical;
  line-height: 1.5;
  overflow: auto;
  font-family: "Roboto", sans-serif;
}

/* Button */
.button {
  padding: 40px 80px; /* 4x larger padding */
  background-color: #3498db;
  color: white;
  font-size: 1rem; /* Keep font size the same (16px) */
  font-weight: bold;
  border: none;
  border-radius: 20px; /* 4x larger border radius */
  cursor: pointer;
  transition: background-color 0.3s;
}
.button:hover {
  background-color: #2980b9;
}

/* Divider */
.divider {
  border: none;
  height: 4px; /* 4x larger height */
  background: #ecf0f1;
  margin: 80px 0; /* 4x larger margin */
}

/* Error Message */
.error {
  border: 4px solid #e74c3c; /* 4x larger border thickness */
  background-color: #f9dcdc;
  color: #c0392b;
  padding: 40px; /* 4x larger padding */
  border-radius: 20px; /* 4x larger border radius */
  font-size: 1rem; /* Keep font size the same (16px) */
  margin-bottom: 80px; /* 4x larger margin */
}

/* Posts Container */
.posts-container {
  display: flex;
  flex-direction: column;
  gap: 60px; /* 4x larger gap */
}

/* Individual Post */
.post {
  background-color: #ecf0f1;
  border-radius: 32px; /* 4x larger border radius */
  padding: 60px; /* 4x larger padding */
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1); /* 4x larger shadow */
  position: relative;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  word-wrap: break-word;
  word-break: break-word;
  text-align: left;
}
.post:hover {
  transform: translateY(-12px); /* 4x larger translation */
  box-shadow: 0 16px 32px rgba(0, 0, 0, 0.15); /* 4x larger shadow */
}
.post-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 40px; /* 4x larger margin */
  font-size: 1rem; /* Keep font size the same (16px) */
  color: #7f8c8d;
}
.post-date {
  font-weight: bold;
}
.post-index {
  font-weight: bold; /* Keep Post Index bold */
}
.post-text {
  font-size: 1rem; /* Keep font size the same (16px) */
  font-weight: normal;
  color: #2c3e50;
  margin: 0;
}
</style>
