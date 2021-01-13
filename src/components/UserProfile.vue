<template>
  <div class="user-profile">
    <div class="user-profile__user-panel">
      <h1 class="user-profile__username">@{{ user.username }}</h1>
      <div class="user-profile__admin-badge" v-if="user.isAdmin">
        Admin
      </div>
      <div class="user-profile__admin-badge" v-else>
        Not Admin
      </div>
      <div class="user-profile__follower-count">
        <strong>Followers: </strong> {{ followers }}
      </div>
      <form class="user-profile__create-twoot" @submit.prevent="createNewTwoot" :class="{ '--exceeded' : maxLength > 180 }">
        <label for="newTwoot"><strong>New Twoot</strong> ({{maxLength}}/ 180)</label>
        <textarea id="newTwoot" rows="4" v-model="newTwootContent" />

        <div class="user-profile__create-twoot-type">
          <label for="newTwoot"><strong>Type:</strong></label>
          <select id="newTwootType" v-model="selectedTwootType">
            <option
              :value="option.value"
              v-for="(option, index) in twootTypes"
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
        v-for="twoot in user.twoots"
        :key="twoot.id"
        :username="user.username"
        :twoot="twoot"
        @favourite="toggleFavourited"
      />
    </div>
  </div>
</template>

<script>
import TwootItem from "../components/TwootItem";

export default {
  name: "UserProfile",
  components: { TwootItem },
  data() {
    return {
      newTwootContent: "",
      selectedTwootType: "instant",
      twootTypes: [
        { value: "draft", name: "Draft" },
        { value: "instant", name: "Instant Twoot" },
      ],
      followers: 0,
      user: {
        id: 1,
        username: "Wyco",
        firstName: "Wycliff",
        lastName: "Kas",
        email: "w4wycliff@gmail.com",
        isAdmin: false,
        twoots: [
          { id: 1, content: "Twitter is amazing" },
          { id: 2, content: "Dont forget to subscribe" },
        ],
      },
    };
  },
  watch: {
    followers(newFollowerCount, oldFollowerCount) {
      if (oldFollowerCount < newFollowerCount) {
        console.log(`${this.user.username} has gained a follower!`);
      }
    },
  },
  computed: {
    maxLength() {
      return this.newTwootContent.length
    }
  },
  methods: {
    followUser() {
      this.followers++;
    },
    toggleFavourited(id) {
      console.log(`Favourited tweet #${id}`);
    },
    createNewTwoot() {
      if (this.newTwootContent && this.selectedTwootType !== "draft") {
        this.user.twoots.unshift({
          id: this.user.twoots.length + 1,
          content: this.newTwootContent,
        });

        this.newTwootContent = "";
      }
    },
  },
  mounted() {
    this.followUser();
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
