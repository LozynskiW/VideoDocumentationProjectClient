<template>
  <q-layout view="lHh Lpr lFf" class="bg-grey-9 text-white">
    <q-header elevated class="bg-grey-6">
      <q-toolbar>
        <q-btn
            flat
            dense
            round
            @click="toggleLeftDrawer"
            aria-label="Menu"
            icon="menu"
        />

        <q-toolbar-title>
          Video Documentation Archive by Wojciech Lozynski
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer
        v-model="leftDrawerOpen"
        show-if-above
        class="bg-grey-8"
    >
      <q-list dark style="margin-top: 150px">
        <q-item-label header>Essential Links</q-item-label>

        <q-item clickable tag="a" target="_blank" rel="noopener" href="http://quasar.dev">
          <q-item-section avatar>
            <q-icon name="school" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Docs</q-item-label>
            <q-item-label caption>https://quasar.dev</q-item-label>
          </q-item-section>
        </q-item>

        <q-item clickable :to="'/'" tag="a">
          <q-item-section avatar>
            <q-icon name="home" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Home</q-item-label>
            <q-item-label caption>Homepage</q-item-label>
          </q-item-section>
        </q-item>

        <q-item clickable tag="a" :to="'/user/projects/public'">
          <q-item-section avatar>
            <q-icon name="video_library" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Public projects</q-item-label>
            <q-item-label caption>Free projects with documentation</q-item-label>
          </q-item-section>
        </q-item>

        <q-separator></q-separator>

        <div v-if="user">
          <q-item clickable tag="a" :to="'/user/projects/owned'">
            <q-item-section avatar>
              <q-icon name="library_books" />
            </q-item-section>
            <q-item-section>
              <q-item-label>Your projects</q-item-label>
              <q-item-label caption>Manage your projects or add new</q-item-label>
            </q-item-section>
          </q-item>

          <q-item clickable tag="a" :to="'/user/projects/accessed'">
            <q-item-section avatar>
              <q-icon name="share" />
            </q-item-section>
            <q-item-section>
              <q-item-label>Accessed projects</q-item-label>
              <q-item-label caption>Show projects you have access to</q-item-label>
            </q-item-section>
          </q-item>

          <q-item clickable tag="a" :to="'/user/project/new'">
            <q-item-section avatar>
              <q-icon name="add" />
            </q-item-section>
            <q-item-section>
              <q-item-label>Add new project</q-item-label>
              <q-item-label caption>Store all documentation for project in one place</q-item-label>
            </q-item-section>
          </q-item>

          <q-item clickable tag="a" :to="`/user/${this.user._id}`">
            <q-item-section avatar>
              <q-icon name="person_pin" />
            </q-item-section>
            <q-item-section>
              <q-item-label>Your profile</q-item-label>
              <q-item-label caption>Manage your account</q-item-label>
            </q-item-section>
          </q-item>

        </div>

        <div v-if="user !== null">
          <q-item clickable tag="a" target="_blank" rel="noopener">
            <q-item-section>
              <q-btn color="negative" icon="remove_circle" label="Logout" @click="logout"/>
            </q-item-section>
          </q-item>
        </div>

        <div v-else>
          <q-item clickable tag="a" :to="'/account'" >
            <q-item-section avatar>
              <q-icon name="person_outline" />
            </q-item-section>
            <q-item-section>
              <q-item-label>Login/Register</q-item-label>
              <q-item-label caption>Log in to manage your projects documentation or register new account</q-item-label>
            </q-item-section>
          </q-item>
        </div>


      </q-list>

      <div v-if="user !== null">
        <q-img class="absolute-top" src="https://media.istockphoto.com/videos/low-poly-dark-background-video-id850926774?s=640x640" style="height: 150px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="70px" class="q-mb-sm">
              <img id="userAvatar" :src="`${user.avatar}`" alt="no image">
            </q-avatar>
            <div class="text-weight-bold">{{user.firstName+" "+user.lastName}}</div>
            <div>{{'@'+user.firstName.charAt(0)+user.lastName}}</div>
          </div>
        </q-img>
      </div>

      <div v-else>

        <q-img class="absolute-top" src="https://media.istockphoto.com/videos/low-poly-dark-background-video-id850926774?s=640x640" style="height: 150px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="70px" class="q-mb-sm">
              <img src="https://cdn.quasar.dev/img/boy-avatar.png">
            </q-avatar>
            <div class="text-weight-bold">Guest</div>
            <div>@welcomedGuest</div>
          </div>
        </q-img>

      </div>

    </q-drawer>

    <q-page-container>
      <router-view/>
      <BottomToolbar/>
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref } from 'vue'
import userService from "@/services/UserService";
import router from "@/router";
import BottomToolbar from "@/components/BottomToolbar";

export default {
  name: 'MyLayout',
  components: {BottomToolbar},
  setup () {
    const leftDrawerOpen = ref(false)
    function toggleLeftDrawer () {
      leftDrawerOpen.value = !leftDrawerOpen.value
    }
    return {
      leftDrawerOpen,
      toggleLeftDrawer
    }
  },
  data () {
    return {
      user: undefined
    }
  },
  async mounted() {

    if (this.$route.query.m) {
      this.message = this.$route.query.m
      alert(this.$route.query.m)
    }

    this.user = await userService.getUserData();
    this.user = JSON.parse(JSON.stringify(this.user))
    sessionStorage.setItem('userId', this.user._id);

  },
  methods: {
    async getUserData() {
      return await userService.getUserData();
    },

    async logout() {
      await userService.logout()
      router.go(1)
    }
  }

}
</script>