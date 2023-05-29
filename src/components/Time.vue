<template>
    <div v-if="location != null && time != null">
        <p><strong>Address: </strong> {{ location.name }}</p>
        <p><strong>Time Zone: </strong> {{ timeZone }}</p>
        <p><strong>Current Time: </strong> {{ time.toLocaleTimeString() }}</p>
    </div>
    <div v-else>
        <p>No search has done yet</p>
    </div>
</template>
  
<script>
export default {
    props: ['location'],

    data() {
        return {
            time: null,
            interval: null,
            timeZone: null
        };
    },

    beforeUnmount() {
        clearInterval(this.interval);
    },

    methods: {
        //get the time from Ninja Api based on lattitude and longitude
        async getTime() {
            clearInterval(this.interval); //reset the interval
            const lat = parseFloat(this.location.latitude);
            const lng = parseFloat(this.location.longitude);

            try {
                const response = await fetch(
                    `https://api.api-ninjas.com/v1/worldtime?lat=${lat}&lon=${lng}`,
                    {
                        headers: {
                            'X-Api-Key': '/OgArA5xYg6unB+8vYOEwA==ovQKtdHSZKmuKPVW'
                        }
                    }
                );
                const data = await response.json();
                const { datetime } = data;
                
                this.timeZone = data.timezone; //set time zone
                this.time = new Date(datetime); //set time
                this.interval = setInterval(this.updateTime, 1000); // Update time every second
            } catch (error) {
                console.error('Error fetching time:', error);
                this.time = null;
            }
        },


        //Increment time by 1 second every second
        updateTime() {
            if (this.time !== null) {
                const currentTime = new Date(this.time);
                currentTime.setSeconds(currentTime.getSeconds() + 1);
                this.time = currentTime;
            }
        }
    },


    watch: {
        location: function () {
            this.getTime();
        }
    }
};
</script>