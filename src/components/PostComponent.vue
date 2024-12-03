<template>
  <div class="background">
    <div class="container">
      <h1 class="header">Posts ✏️</h1>

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
            <span class="post-index"><strong>Post {{ index + 1 }}</strong></span>
          </div>
          <p class="post-text">{{ post.text }}</p>
        </div>
      </div>

      <!-- Toast Notification -->
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
  background-color: #f3f4f6; /* Light gray background */
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

/* Container */
.container {
  max-width: 1600px; /* Set a wider fixed width for the container */
  width: 90%; /* Responsive width */
  padding: 30px;
  background: #ffffff; /* White background for the container */
  border-radius: 15px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2); /* Floating effect */
  font-family: "Roboto", sans-serif;
}

/* Header */
.header {
  text-align: center;
  font-size: 1.5rem; /* Reduced font size to 24px */
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

/* Textarea Styles */
.textarea {
  width: 100%; /* Full width within container */
  height: auto;
  min-height: 120px;
  padding: 10px;
  border: 1px solid #dcdde1;
  border-radius: 5px;
  font-size: 1rem; /* Match font size of posts (16px) */
  resize: vertical; /* Allows vertical resizing by the user */
  line-height: 1.5;
  overflow: auto;
  font-family: "Roboto", sans-serif; /* Match font family of posts */
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

/* Toast Notification */
.toast {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 15px 20px;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  font-size: 14px;
  font-weight: bold;
  animation: fadein 0.5s, fadeout 0.5s 2.5s;
}
.toast.success {
  background-color: #2ecc71; /* Green for success */
  color: white;
}
.toast.error {
  background-color: #e74c3c; /* Red for error */
  color: white;
}

/* Toast Animation */
@keyframes fadein {
  from {
    opacity: 0;
    bottom: 10px;
  }
  to {
    opacity: 1;
    bottom: 20px;
  }
}
@keyframes fadeout {
  from {
    opacity: 1;
    bottom: 20px;
  }
  to {
    opacity: 0;
    bottom: 10px;
  }
}

/* Posts Container */
.posts-container {
  display: flex;
  flex-direction: column;
  gap: 15px;
  height: 500px; /* Fixed height */
  overflow-y: auto; /* Adds scrolling for overflow */
  border: 1px solid #dcdde1;
  padding: 10px;
  border-radius: 10px;
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
  word-wrap: break-word; /* Ensures text wraps properly */
  word-break: break-word;
  text-align: left; /* Align all posts to the left */
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
.post-index {
  font-weight: bold; /* Bold the Post X text */
}
.post-text {
  font-size: 1rem; /* Match font size to textarea (16px) */
  font-weight: normal;
  color: #2c3e50;
  margin: 0;
}
</style>
