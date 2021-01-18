<template>
  <div class="user-profile">
    <div class="user-profile__user-panel">
      <h1 class="user-profile__username">@{{ state.user.username }}</h1>
      <div class="user-profile__admin-badge" v-if="state.user.isAdmin">
        Admin
      </div>
      <div class="user-profile__admin-badge" v-else>
        Not Admin
      </div>
      <div class="user-profile__follower-count">
        <strong>Followers: </strong> {{ state.followers }}
      </div>
      <form
        class="user-profile__create-twoot"
        @submit.prevent="createNewTwoot"
        :class="{ '--exceeded': twootCount > 180 }"
      >
        <label for="newTwoot"
          ><strong>New Twoot</strong> ({{ twootCount }}/ 180)</label
        >
        <textarea id="newTwoot" rows="4" v-model="state.newTwootContent" />

        <div class="user-profile__create-twoot-type">
          <label for="newTwoot"><strong>Type:</strong></label>
          <select id="newTwootType" v-model="state.selectedTwootType">
            <option
              :value="option.value"
              v-for="(option, index) in state.twootTypes"
              :key="index"
            >
              {{ option.name }}
            </option>
          </select>
        </div>
        <button>
          Twoot!
        </button>
      </form>
    </div>
    <div class="user-profile__twoots-wrapper">
      <TwootItem
        class="user-profile__twoots"
        v-for="twoot in state.user.twoots"
        :key="twoot.id"
        :username="state.user.username"
        :twoot="twoot"
        @favourite="toggleFavourited"
      />
    </div>
  </div>
</template>

<script>
import { reactive, computed, onMounted } from "vue";
import TwootItem from "@/components/TwootItem";
import { useRoute } from "vue-router";
import { users } from "../assets/users";

export default {
  name: "UserProfile",
  components: { TwootItem },

  setup() {
    const route = useRoute();

    const userId = computed(() => route.params.userId);

    const state = reactive({
      newTwootContent: "",
      selectedTwootType: "instant",
      twootTypes: [
        { value: "draft", name: "Draft" },
        { value: "instant", name: "Instant Twoot" },
      ],
      followers: 0,
      user: users[userId.value - 1] || users[0],
    });

    const twootCount = computed(() => state.newTwootContent.length);

    const followUser = () => {
      state.followers++;
    };

    const toggleFavourited = (id) => {
      console.log(`Favourited tweet #${id}`);
    };

    const createNewTwoot = () => {
      if (state.newTwootContent && state.selectedTwootType !== "draft") {
        state.user.twoots.unshift({
          id: state.user.twoots.length + 1,
          content: state.newTwootContent,
        });

        state.newTwootContent = "";
      }
    };

    onMounted(followUser);

    return {
      state,
      twootCount,
      userId,
      followUser,
      toggleFavourited,
      createNewTwoot,
    };
  },
};
</script>

<style lang="scss">
.user-profile {
  display: grid;
  grid-template-columns: 1fr 3fr;
  width: 100%;
  padding: 50px 5%;

  .user-profile__user-panel {
    display: flex;
    flex-direction: column;
    margin-right: 50px;
    padding: 20px;
    background-color: white;
    border-radius: 5px;
    border: 1px solid #dfe3e8;

    h1 {
      margin: 0;
    }

    .user-profile__admin-badge {
      background: rebeccapurple;
      color: white;
      border-radius: 5px;
      margin-right: auto;
      padding: 0 10px;
      font-weight: bold;
      margin-bottom: 20px;
    }

    .user-profile__create-twoot {
      padding-top: 20px;
      display: flex;
      flex-direction: column;

      &.--exceeded {
        color: red;

        button {
          background-color: red;
          border: none;
          color: white;
        }
      }
    }
  }

  .user-profile__twoots-wrapper {
    display: grid;
    grid-gap: 10px;
  }
}
</style>
