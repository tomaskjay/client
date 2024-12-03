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
      <transition name="toast">
        <div v-if="toastMessage" :class="['toast', toastType]">{{ toastMessage }}</div>
      </transition>

      <!-- Dark Mode Toggle -->
      <div class="dark-mode-toggle">
        <label class="switch">
          <input type="checkbox" v-model="isDarkMode" />
          <span class="slider"></span>
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
      toastType: "",
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
        this.showToast("Post cannot be empty!", "error");
        return;
      }
      try {
        await PostService.insertPost(this.text);
        this.posts = await PostService.getPosts();
        this.text = ""; // Clear the text box
        this.error = ""; // Clear any previous error
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
  background-color: #121212; /* Slightly lighter dark background */
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  transition: background-color 0.3s;
}
.background.dark {
  background-color: #1c1c1c;
}

/* Container */
.container {
  max-width: 1800px;
  margin: 0 auto;
  padding: 30px;
  background: #ffffff;
  border-radius: 15px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
  font-family: "Roboto", sans-serif;
  transition: background-color 0.3s, color 0.3s;
}
.container.dark {
  background: #243447; /* Navy blue background */
  color: #f0f0f0;
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

/* Toast Animations */
.toast-enter-active,
.toast-leave-active {
  transition: all 0.5s ease;
}
.toast-enter {
  transform: translateX(100%);
  opacity: 0;
}
.toast-leave-to {
  transform: translateX(100%);
  opacity: 0;
}

/* Dark Mode Toggle */
.dark-mode-toggle {
  position: absolute;
  bottom: 20px;
  right: 20px;
}

/* Toggle Switch */
.switch {
  position: relative;
  display: inline-block;
  width: 50px;
  height: 25px;
}
.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  transition: 0.4s;
  border-radius: 25px;
}
.slider:before {
  position: absolute;
  content: "";
  height: 19px;
  width: 19px;
  left: 3px;
  bottom: 3px;
  background-color: white;
  transition: 0.4s;
  border-radius: 50%;
}
input:checked + .slider {
  background-color: #3498db;
}
input:checked + .slider:before {
  transform: translateX(25px);
}
</style>
