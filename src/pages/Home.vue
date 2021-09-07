<template>
    <div class="wrapper-content wrapper-content--fixed">
        <section>
            <div class="container">

                <!-- errors -->
                <div class="error" v-if="error" style="margin-bottom: 20px;">
                    <p>{{ error }}</p>
                </div>

                <!-- search -->
                <search 
                    :value="search"
                    placeholder="Type username..."
                    @search="search = $event"/>

                <!-- buttons -->
                <button v-if="!repos" class="btn btnPrimary" @click="getRepos">Search!</button>
                <button v-else class="btn btnPrimary" @click="getRepos">Search again!</button>

                <!-- wrapper -->
                <div class="repo-list" v-if="repos">
                    <!-- item -->
                    <div class="repo-item" v-for="repo in repos" :key="repo.id">
                        <div class="repo-info">
                            <a class="link" target="_blank" :href="repo.html_url">{{ repo.name }}</a>
                            <span>{{ repo.stargazers_count }} ⭐</span>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
import search from '@/components/Search.vue'
import axios from 'axios'

export default {
    components: {
        search
    },
    data () {
        return {
            search: '',
            error: null,
            repos: null
        }
    },
    methods: {
        getRepos () {
            axios
                .get(`https://api.github.com/users/${this.search}/repos`)
                    .then(res => {
                        this.error = null
                        this.repos = res.data
                    })
                    .catch(err => {
                        console.log(err)
                        this.repos = null
                        this.error = 'Can`t find this user'
                    })
        }
    }
}
</script>

<style lang="scss" scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
}

button {
    margin-top: 40px;
}

.repo-list {
    width: 400px;
    margin: 30px 0;
}

.repo-info {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    padding: 10px 0;
    border-bottom: 1px solid #dbdbdb;
}
</style>