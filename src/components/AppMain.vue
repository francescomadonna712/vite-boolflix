<script>
import AppCard from "./AppCard.vue";

import AppCardSerie from "./AppCardSerie.vue";

export default {
    components: {
        AppCard,
        AppCardSerie,
    },
    name: "AppMain",
    data() {
        return {

        }
    }
}
</script>

<template>
    <div class="main-body">
        <AppCard />
        <AppCardSerie />
    </div>

</template>


<style scoped></style>