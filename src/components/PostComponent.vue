<template>
  <div class="background">
    <div class="container">
      <h1 class="header">Blog Posts ✏️</h1>

      <div class="create-post">
        <div class="input-container">
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

      <div v-if="posts.length" class="posts-container">
        <div
          class="post"
          v-for="(post, index) in posts"
          v-bind:key="post._id"
          v-on:dblclick="deletePost(post._id)"
        >
          <div class="post-header">
            <span class="post-index">Post {{ index + 1 }}</span>
            <span class="post-date">
              {{
                new Date(post.createdAt).toLocaleDateString('en-US', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </span>
          </div>
          <p class="post-text">{{ post.text }}</p>
        </div>
      </div>

      <div v-else class="empty-state">No posts yet. Start writing!</div>

      <p class="note">This project was made with Vue, Express, and MongoDB.</p>

      <div v-if="showToast" :class="`toast ${toastType}`">{{ toastMessage }}</div>
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
      showToast: false,
      toastMessage: "",
      toastType: "success", // 'success' or 'error'
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
      if (this.text.trim() === "") {
        this.error = "Post cannot be empty!";
        return;
      }
      try {
        await PostService.insertPost(this.text);
        this.posts = await PostService.getPosts();
        this.text = ""; // Clear the text box
        this.error = ""; // Clear the error message
        this.showToastNotification("Post submitted successfully!", "success");
      } catch (err) {
        this.error = err.message;
      }
    },
    async deletePost(id) {
      try {
        await PostService.deletePost(id);
        this.posts = await PostService.getPosts();
        this.showToastNotification("Post deleted successfully!", "error");
      } catch (err) {
        this.error = err.message;
      }
    },
    showToastNotification(message, type) {
      this.toastMessage = message;
      this.toastType = type;
      this.showToast = true;
      setTimeout(() => {
        this.showToast = false;
      }, 3000); // Hide after 3 seconds
    },
  },
};
</script>

<style scoped>
/* Background */
.background {
  background-color: #f3f4f6;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

/* Container */
.container {
  width: 70%; /* Decreased width by 30% */
  max-width: 800px;
  padding: 30px;
  background: #ffffff;
  border-radius: 15px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
  font-family: "Roboto", sans-serif;
}

/* Header */
.header {
  text-align: center;
  font-size: 1.5rem;
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

.textarea {
  width: 100%;
  min-height: 120px;
  padding: 10px;
  border: 1px solid #dcdde1;
  border-radius: 5px;
  font-size: 1rem;
  resize: vertical;
  line-height: 1.5;
  overflow: auto;
  font-family: "Roboto", sans-serif;
}

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

/* Posts Container */
.posts-container {
  display: flex;
  flex-direction: column;
  gap: 15px;
  padding: 10px;
  border-radius: 10px;
  overflow-y: auto;
  background-color: #f3f4f6;
}

/* Empty State */
.empty-state {
  text-align: center;
  color: #7f8c8d;
  font-size: 0.9rem;
  margin: 20px 0;
}

/* Post */
.post {
  background-color: #ecf0f1;
  border-radius: 8px;
  padding: 15px;
  word-wrap: break-word;
  word-break: break-word;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Post Header */
.post-header {
  display: flex;
  justify-content: space-between;
  width: 100%;
  color: #7f8c8d;
  font-size: 0.9rem;
}
.post-index {
  font-weight: bold;
}
.post-date {
  color: #2c3e50;
  font-size: 0.9rem;
}

/* Note */
.note {
  text-align: center;
  font-size: 0.75rem;
  color: #7f8c8d;
  margin-top: 20px;
}
</style>
