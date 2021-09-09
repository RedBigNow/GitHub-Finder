<template>
    <div class="wrapper-content wrapper-content--fixed">
        <section>
            <div class="container">

                <!-- errors -->
                <div class="error" v-if="error" style="margin-bottom: 20px;">
                    <p>{{ error }}</p>
                </div>

                <!-- search -->
                <form class="search-form">
                    <search 
                        :value="search"
                        placeholder="Enter GitHub login..."
                        @search="search = $event"/>
                    <button class="btn btnPrimary" @click="getRepos">Search</button>
                </form>

                <!-- user -->
                <div class="user__wrapper" v-if="user.html_url">
                    <div class="user__avatar">
                        <img :src="user.avatar_url">
                    </div>
                    <div class="user__info">
                        <p class="user__info-name">{{ user.login }} ({{ user.name }})</p>
                        <p class="user__info-repos">Public repositories: <span>{{ user.public_repos }}</span></p>
                        <a class="user__info-link" target="_blank" :href="user.html_url">View profile >></a>
                    </div>
                    
                </div>

                <!-- repositories -->
                <div class="repo-list" v-if="repos">
                    <!-- item -->
                    <div class="repo-item" v-for="repo in repos" :key="repo.id">
                        <div class="repo-info">
                            <a class="link" target="_blank" :href="repo.html_url">{{ repo.name }}</a>
                            <span v-if="repo.language">{{ repo.language }} 💡</span>
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
            repos: null,
            user: []
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
            axios
                .get(`https://api.github.com/users/${this.search}`)
                    .then(res => {
                        this.error = null
                        this.user = res.data
                    })
                    .catch(err => {
                        console.log(err)
                        this.user = []
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

form.search-form {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: space-between;
    width: 500px;
}

.user__wrapper {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    width: 500px;
    padding: 20px;
    margin-top: 40px;
    background: #fff;
    box-shadow: 0 0 10px 8px rgba(0, 0, 0, 0.05);
}

.user__avatar {
    width: 25%;
    border-radius: 50%;
    overflow: hidden;
}

.user__info {
    padding-left: 25px;
}

.user__info-name {
    padding-bottom: 10px;
    font-weight: 600;
}

.user__info-repos {
    padding-bottom: 10px;

}

.repo-list {
    width: 500px;
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

@media screen and (max-width: 576px) {
    form.search-form,
    .user__wrapper,
    .repo-list {
        width: 100%;
    }
}

@media screen and (max-width: 480px) {
    form.search-form {
        justify-content: center;

        button {
            margin-top: 20px;
        }
    }

    .wrapper__search {
        width: 100%;
    }

    .user__wrapper {
        justify-content: center;
    }

    .user__avatar {
        width: 100%;
        border-radius: 0;
        text-align: center;

        img {
            width: 82px;
            border-radius: 50%;
        }
    }

    .user__info {
        padding-left: 0;
        padding-top: 15px;
        text-align: center;
    }

    .repo-info {
        font-size: 14px;
    }
}
</style>