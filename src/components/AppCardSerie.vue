<script>
import axios from 'axios';

export default {
    data() {
        return {
            tvShows: [],
            currentPage: 1,
            totalPages: 1,
            loading: false,
            searchQuery: '', // 
        };
    },
    mounted() {
        this.fetchTVShows();
    },
    methods: {
        fetchTVShows(page = 1) {
            if (this.loading || page > this.totalPages) return;

            this.loading = true;
            const options = {
                method: 'GET',
                url: 'https://api.themoviedb.org/3/search/tv',
                params: { query: this.searchQuery, include_adult: 'false', language: 'it-IT', page: page, api_key: '57880bab5f08b4351e407906454d0666' },
                headers: {
                    accept: 'application/json',
                }
            };

            axios.request(options)
                .then((response) => {
                    if (page === 1) {
                        this.tvShows = response.data.results;
                    } else {
                        this.tvShows = [...this.tvShows, ...response.data.results];
                    }
                    this.totalPages = response.data.total_pages;
                    this.currentPage = page;
                    this.loading = false;
                })
                .catch((error) => {
                    console.error(error);
                    this.loading = false;
                });
        },
        searchTVShows() {
            this.currentPage = 1;
            this.totalPages = 1;
            this.tvShows = [];
            this.fetchTVShows();
        },
        loadMore() {
            if (this.currentPage < this.totalPages) {
                this.fetchTVShows(this.currentPage + 1);
            }
        },
        getImageUrl(path) {
            return path ? `https://image.tmdb.org/t/p/w342${path}` : 'path/to/default/image.jpg';
        }
    }
};

</script>

<template>
    <div>
        <h1>Serie TV</h1>
        <div class="movie-sc"> <input v-model="searchQuery" placeholder="Cerca una serie TV..." />
            <button @click="searchTVShows">Cerca</button>
        </div>

        <ul>
            <li v-for="tvShow in tvShows" :key="tvShow.id">
                <div class="container-card"></div>
                <div class="card">
                    <img :src="getImageUrl(tvShow.poster_path)" alt="Immagine non disponibile" />
                </div>
                <div class="info-card">
                    <h4>{{ tvShow.name }}</h4>
                    <p><strong>Nome Originale:</strong> {{ tvShow.original_name }}</p>
                    <p><strong>Voto Medio:</strong> {{ tvShow.vote_average }}</p>
                    <p><strong>Descrizione:</strong> {{ tvShow.overview }}</p>
                </div>


            </li>
        </ul>
        <div class="up-more">
            <button v-if="currentPage < totalPages" @click="loadMore">Carica più</button>
        </div>
    </div>

</template>

<style scoped>
.info-card {
    justify-content: center;
    align-items: center;
    text-align: center;
    width: 20rem;
    border-radius: 0;
    background-color: black;
}

.card {
    justify-content: center;
    align-items: center;
    text-align: center;
    width: 20rem;
    border-radius: 0;

}

ul {
    justify-content: center;
    gap: 2rem;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    list-style-type: none;
    padding: 0;
}

li {
    margin: 20px 0;
}

img {
    display: block;
    width: 20rem;
    display: block;

}

.movie-sc {
    padding: 2rem;
    justify-content: space-between;
}

.up-more {
    padding: 2rem;
}

.info-card p {
    color: white;
    margin: 5px 0;
}
</style>
