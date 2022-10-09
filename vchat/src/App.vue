<template>
  <div class="container">
    <div class="d-flex flex-column align-items-stretch flex-shrink-0 bg-white">
      <div
        class="d-flex align-items-center flex-shrink-0 p-3 link-dark text-decoration-none border-bottom"
      >
        <input class="form-control m-2" v-model="username" />
      </div>
      <div class="list-group list-group-flush border-bottom scrollarea">
        <div
          class="list-group-item list-group-item-action py-3 lh-sm"
          v-for="message in messages"
          :key="message"
        >
          <div
            class="d-flex w-100 align-items-center justify-content-between bg-secondary text-white"
          >
            <strong class="mb-1">{{ message.username }}</strong>
          </div>
          <div class="col-5 mb-5 small">{{ message.message }}</div>
        </div>
      </div>
    </div>
    <form @submit.prevent="submit">
      <input
        class="form-control m-2"
        placeholder="write a message"
        v-model="message"
      />
    </form>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import Pusher from "pusher-js";
export default {
  name: "App",
  setup() {
    const username = ref("username");
    const messages = ref([]);
    const message = ref("");

    onMounted(() => {
      Pusher.logToConsole = true;

      const pusher = new Pusher("91615b389371a341d125", {
        cluster: "ap1",
      });

      const channel = pusher.subscribe("chat");
      channel.bind("message", (data) => {
        messages.value.push(data);
      });
    });

    const submit = async () => {
      await fetch("http://127.0.0.1:8000/api/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          username: username.value,
          message: message.value,
        }),
      });

      message.value = "";
    };

    return {
      username,
      messages,
      message,
      submit,
    };
  },
};
</script>

<style>
.scrollarea {
  min-height: 500px;
}
</style>
