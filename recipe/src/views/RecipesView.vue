<script setup>
    import RecipeItem from '@/components/RecipeItem.vue'
    import Carousel from '@/components/Carousel.vue'
</script>

<template>
    <section>
        <main class="bg-light">
        <Carousel />
        <div class="album py-5 bg-light">
            <div class="container">
            <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-3">
                <RecipeItem id="jamie1" />
                <RecipeItem id="jamie2" />
                <RecipeItem id="jamie3" />
                <RecipeItem id="ramsay1" />
                <RecipeItem id="ramsay2" />
            </div>
            </div>
        </div>
        </main>

        <footer class="container">
            <br/>
            <p class="float-end"><a href="#" class="btn btn-primary btn-sm">Back to top</a></p>
            <p>&copy; 2022 AnyCompany, Inc. &middot; <a href="#">Privacy</a> &middot; <a href="#">Terms</a></p>
        </footer>
    </section>
</template>
