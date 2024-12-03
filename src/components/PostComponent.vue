<template>
  <div :class="['background', { dark: isDarkMode }]">
    <div :class="['container', { dark: isDarkMode }]">
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
      <div v-if="toastMessage" :class="['toast', toastType]">{{ toastMessage }}</div>

      <!-- Dark Mode Toggle -->
      <div class="dark-mode-toggle">
        <label>
          <input type="checkbox" v-model="isDarkMode" />
          Dark Mode
        </label>
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
      toastMessage: null,
      toastType: "", // Controls toast notification style
      isDarkMode: false, // Dark mode toggle state
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
        this.showToast("Post submitted successfully!", "success");
      } catch (err) {
        this.error = "Failed to submit post. Please try again.";
      }
    },
    async deletePost(id) {
      try {
        await PostService.deletePost(id);
        this.posts = await PostService.getPosts();
        this.showToast("Post deleted successfully!", "error");
      } catch (err) {
        this.error = "Failed to delete post. Please try again.";
      }
    },
    showToast(message, type) {
      this.toastMessage = message;
      this.toastType = type;
      setTimeout(() => {
        this.toastMessage = null;
        this.toastType = "";
      }, 3000); // Hide the toast after 3 seconds
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
  transition: background-color 0.3s;
}
.background.dark {
  background-color: #000000; /* Dark mode background */
}

/* Container */
.container {
  max-width: 1800px; /* Wider container */
  margin: 0 auto;
  padding: 30px;
  background: #ffffff; /* Light mode background */
  border-radius: 15px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2); /* Floating effect */
  font-family: "Roboto", sans-serif;
  transition: background-color 0.3s, color 0.3s;
}
.container.dark {
  background: #001f3f; /* Navy blue background */
  color: #f0f0f0; /* Light font color */
}

/* Header */
.header {
  text-align: center;
  font-size: 1.5rem; /* Reduced font size to 24px */
  color: #2c3e50;
  margin-bottom: 20px;
  font-weight: bold;
}
.background.dark .header {
  color: #f0f0f0; /* Header color in dark mode */
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
  width: 85%; /* Wider text box */
  height: auto;
  min-height: 120px;
  padding: 10px;
  border: 1px solid #dcdde1;
  border-radius: 5px;
  font-size: 1rem;
  resize: vertical;
  line-height: 1.5;
  overflow: auto;
  font-family: "Roboto", sans-serif;
  transition: background-color 0.3s, color 0.3s;
}
.background.dark .textarea {
  background-color: #1e1e1e;
  color: #f0f0f0;
  border: 1px solid #333333;
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
.background.dark .button {
  background-color: #005cbf;
}
.background.dark .button:hover {
  background-color: #004494;
}

/* Divider */
.divider {
  border: none;
  height: 1px;
  background: #ecf0f1;
  margin: 20px 0;
}
.background.dark .divider {
  background: #333333;
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
  color: white;
}
.toast.success {
  background: #28a745; /* Green for success */
}
.toast.error {
  background: #dc3545; /* Red for error */
}

/* Dark Mode Toggle */
.dark-mode-toggle {
  position: absolute;
  bottom: 20px;
  right: 20px;
  font-size: 14px;
  color: #2c3e50;
}
.background.dark .dark-mode-toggle {
  color: #f0f0f0;
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
  word-wrap: break-word;
  word-break: break-word;
  text-align: left;
}
.background.dark .post {
  background-color: #1e1e1e;
  color: #f0f0f0;
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
.background.dark .post-header {
  color: #cccccc;
}
.post-date {
  font-weight: bold;
}
.post-index {
  font-weight: bold;
}
.post-text {
  font-size: 1rem;
  font-weight: normal;
  color: #2c3e50;
  margin: 0;
}
.background.dark .post-text {
  color: #f0f0f0;
}
</style>
