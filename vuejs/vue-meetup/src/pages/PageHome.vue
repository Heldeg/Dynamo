<template>
  <div>
    <AppHero />
    <div v-if="pageLoader_isDataLoaded" class="container">
      <section class="section">
      <div class="m-b-lg">
        <h1 class="title is-inline">Reuniones cerca de ti</h1>
        <AppDropdown />
        <router-link v-if="user" :to="{name: 'PageMeetupCreate'}" 
                     class="button is-primary is-pulled-right m-r-sm">Crear Meetup</router-link>
        <router-link :to="{name: 'PageMeetupFind'}" 
                     class="button is-primary is-pulled-right m-r-sm">Todas las Reuniones
        </router-link>
      </div>
      <div class="row columns is-multiline">
        <!--iterar de aca-->
        <MeetupItem v-for="meetup in meetups" :key="meetup._id" :meetup="meetup"/>
      </div>
      </section>
      <section class="section">
        <div>
          <h1 class="title">Categorias</h1>
          <div class="columns cover is-multiline is-mobile">
            <!--Categorias-->
            <CategoryItem v-for="category in categories" :key="category._id" :category="category"/>
          </div>
        </div>
      </section>
      </div>
    <div v-else class="container">
      <AppSpinner />
    </div>
  </div>
</template>

<script>
import axios from "axios"
import CategoryItem from '@/components/CategoryItem'
import MeetupItem from '@/components/MeetupItem'
import { mapActions, mapState, mapGetters } from 'vuex'
import pageLoader from '@/mixins/pageLoader'

//import Vue from 'vue'
  export default {
    components: {
      CategoryItem,
      MeetupItem
    },
     mixins: [pageLoader],
    computed: {
      ...mapGetters({
        'user': 'auth/authUser'
      }),
      ...mapState({
        meetups: state => state.meetups.items,
        categories: state => state.categories.items
      })
    },
    created () {
      Promise.all([this.fetchMeetups(), this.fetchCategories()])
        .then(() => this.pageLoader_resolveData())
        .catch((err) => {
          console.error(err)
          this.pageLoader_resolveData()
        })
    },
    methods: {
      ...mapActions('meetups', ['fetchMeetups']),
      ...mapActions('categories', ['fetchCategories'])
    }
  }
</script>

<style scoped>
</style>
