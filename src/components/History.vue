<template>
    <div>
        <h3 style="display: inline;">Searched History</h3>
        <!-- Delete button -->
        <button style="margin-left: 15px; margin-bottom: 10px;" @click="deleteSelectedLocations">Delete Selected</button>
        <!-- List -->
        <ul v-if="paginatedLocations.length !== 0">
            <li v-for="location in paginatedLocations" :key="location.name">
                <input type="checkbox" v-model="selectedLocations" :value="location.name"
                    :id="`${location.longitude.toString() + location.latitude.toString()}`">
                <label :for="`${location.longitude.toString() + location.latitude.toString()}`">{{ location.name }}</label>
            </li>
        </ul>
        <p v-else>No search has been done yet</p>
        
        <!-- Pagination -->
        <div class="pagination">
            <button @click="goToPage(currentPage - 1)" :disabled="currentPage === 1">&lt; Previous</button>
            <button v-for="page in displayedPages" :key="page" @click="goToPage(page)"
                :class="{ active: page === currentPage }">{{ page }}</button>
            <button @click="goToPage(currentPage + 1)" :disabled="currentPage === totalPages">Next ></button>
        </div>
    </div>
</template>
  
<script>
export default {

    props: ['searchedLocations'],


    data() {
        return {
            selectedLocations: [],
            currentPage: 1,
            itemsPerPage: 10
        };
    },


    computed: {
        //calculate total pages
        totalPages() {
            return Math.ceil(this.searchedLocations.length / this.itemsPerPage);
        },
        //create the pagination array
        paginatedLocations() {
            const startIndex = (this.currentPage - 1) * this.itemsPerPage;
            const endIndex = startIndex + this.itemsPerPage;
            return this.searchedLocations.slice(startIndex, endIndex);
        },
        //create the page number array
        displayedPages() {
            const startPage = Math.max(this.currentPage - 1, 1);
            const endPage = Math.min(this.currentPage + 1, this.totalPages);
            return Array(endPage - startPage + 1)
                .fill()
                .map((_, index) => startPage + index);
        }
    },


    methods: {
        //deleted selected locations
        deleteSelectedLocations() {
            this.$emit('deleteLocations', this.selectedLocations);
            this.selectedLocations = [];
        },
        //jumps to the selected page
        goToPage(page) {
            if (page >= 1 && page <= this.totalPages) {
                this.currentPage = page;
            }
        }
    }
};
</script>
  
<style>
.pagination {
    margin-top: 10px;
}

.pagination button {
    margin-right: 5px;
}

.pagination .active {
    background-color: white;
    border-radius: 100px;
    color: black;
}
</style>