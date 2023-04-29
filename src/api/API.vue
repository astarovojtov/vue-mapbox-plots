<script>
import axios from "axios";
const API_URL = import.meta.env.VITE_API_URL;
const API_TOKEN = import.meta.env.VITE_STRAPI_API_TOKEN;

export default {
    getPlots: function() {
        return axios.get(`${API_URL}/plots`)
    },
    createPlots: createPlots,

    createArea: function(draw) {
        const data = draw.getAll();
        if (data.features.length === 0) {
            return
        }

        const lastPlot = data.features.shift();
        createPlots({
            mapbox_feature_id: lastPlot.id,
            coordinates: lastPlot.geometry.coordinates
        }).then( () => {
            this.$emit('apiResponded', {
                status: 'Success', 
                message: 'Plot was successfully created and persisted in database'
            });
        })
        .catch( err => {
            this.$emit('apiResponded', {
                status: 'Error',
                message: 'Error on creating plot'
            })
        });
    }
}

function createPlots(data) {
        return axios.post(`${API_URL}/plots`, { 
            data: data
        }, {
            headers: {
            Authorization: `Bearer ${API_TOKEN}`
        }
        })
    }
</script>