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
        },
        getStars(vote) {
            const fullStars = Math.floor(vote / 2); // Assume il voto è su una scala di 10, quindi lo dividi per 2 per ottenere stelle su una scala di 5
            const halfStar = vote % 2 >= 1 ? 1 : 0; // Verifica se c'è una mezza stella
            const emptyStars = 5 - fullStars - halfStar; // Calcola il numero di stelle vuote
            return '★'.repeat(fullStars) + '☆'.repeat(emptyStars);
        }
    }
};

</script>

<template>
    <div>
        <h1>Serie TV</h1>
        <div class="series-sc"> <input v-model="searchQuery" placeholder="Cerca una serie TV..." />
            <button @click="searchTVShows">Cerca</button>
        </div>

        <ul class="cards">
            <li v-for="tvShow in tvShows" :key="tvShow.id" class="single-card">
                <div class="img-card">
                    <img :src="getImageUrl(tvShow.poster_path)" alt="Immagine non disponibile" />
                    <div class="info-card">
                        <h4>{{ tvShow.name }}</h4>
                        <p><strong>Nome Originale:</strong> {{ tvShow.original_name }}</p>
                        <p><strong>Voto Medio:</strong><span class="star" v-html="getStars(tvShow.vote_average)"></span>
                            {{
                                tvShow.vote_average }}</p>
                        <p><strong>Descrizione:</strong> {{ tvShow.overview }}</p>
                    </div>
                </div>
            </li>
        </ul>
        <div class="up-more">
            <button v-if="currentPage < totalPages" @click="loadMore">Carica più</button>
        </div>
    </div>

</template>

<style scoped>
.container-card {
    position: relative;
}

.info-card {
    justify-content: center;
    align-items: center;
    text-align: center;
    width: 20rem;
    border-radius: 0;
    background-color: black;
    width: 100%;
}

.img-card {
    max-width: 100%;
    height: auto;
    display: block;
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
    position: relative;
    overflow: hidden;
    margin: 20px 0;
}

.info-card p {
    color: white;
    text-align: center;
}

.show-image {
    max-width: 100%;
    height: auto;
    display: block;

}

.info-card {

    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.8);
    opacity: 0;
    padding: 20px;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
}

.img-card:hover .show-image {
    opacity: 0;
}

.img-card:hover .info-card {
    opacity: 1;
}

h4 {
    color: white;
}

strong {
    color: white;
}

.star {
    color: orange;
}

h1 {
    color: white;
}
</style>
