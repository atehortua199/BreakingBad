<template>
    <div class="cQuotes">
        <!--Dialog-->
        <v-dialog
            v-model="loading.state"
            hide-overlay
            persistent
            width="400"
        >
            <v-card
            class="pt-2"
            :color="loading.color"
            dark
            >
            <v-card-text>
                {{loading.title}}
                <v-progress-linear
                indeterminate
                color="white"
                class="mb-0"
                ></v-progress-linear>
            </v-card-text>
            </v-card>
        </v-dialog>
        <!--Dialog-->

        <h1 class="font-weight-light mb-4">Quotes</h1>
        
        <v-data-table
        no-data-text="No data at the moment"
        :headers="headers"
        :items="data"
        hide-default-footer
        disable-sort
        class="elevation-3 mb-4"
        >
            <template v-slot:item.quote_id="{ item }">  
                <v-btn 
                icon
                :to="{ name: 'QuotesId', params: { id: item.quote_id } }">
                    <v-icon 
                    color="blue accent-4"
                    >visibility
                    </v-icon>
                </v-btn>
            </template>
        </v-data-table>

        <v-card class="elevation-0 d-flex justify-end">
            <v-btn 
            v-if="s > 6"
            fab dark 
            small 
            :disabled="btnDisabled"
            color="blue darken-4"
            @click="getQuotes(0)"
            >
                <v-icon dark>keyboard_arrow_left</v-icon>
            </v-btn>
            <v-btn 
            fab dark 
            small 
            :disabled="btnDisabled"
            color="blue darken-4"
            class="ml-3"
            @click="getQuotes(1)"
            v-if="data.length > 0 || this.s == 31"
            >
                <v-icon dark>keyboard_arrow_right</v-icon>
            </v-btn>
        </v-card>
    </div>
</template>

<script>
import Axios from "axios";
import { mapState } from "vuex";
import { mapMutations } from "vuex";

export default {
    data() {
        return {
            s: 1,
            f: 5,
            btnDisabled: false,
            headers: [
                { text: 'Quote', value: 'quote', align: 'center' },
                { text: 'Author', value: 'author', align: 'center' },
                { text: 'Show more', value: 'quote_id', align: 'center' }
            ],
            data: []
        }
    },
    methods: {
        async getQuotes(np) {
            this.data = [];
            if(np == 0) {
                this.s = this.s - 10;
                this.f = this.f - 10;
            }
            try {
                this.showLoading({title: 'Please wait...', color: 'info'});
                this.btnDisabled = true;

                let responsePromises = [];
                let responsePromise;

                 for (this.s; this.s <= this.f; this.s++) {
                    responsePromise = Axios.get(`https://www.breakingbadapi.com/api/quotes/${this.s}`);
                    responsePromises.push(responsePromise);
                }

                let responses = await Promise.all(responsePromises);

                let preData = [];
                for (let i = 0; i < responses.length; i++) {
                    preData.push(responses[i].data[0]);
                }
                this.data = preData

            } catch(error) {
                console.log(error);
            } finally {
                this.btnDisabled = false;
                this.hiddenLoading({color: 'info'});
            }
            this.f = this.f + 5;
        },
        ...mapMutations(['showLoading', 'hiddenLoading'])
    },
    computed: {
        ...mapState(['loading'])
    },
    mounted() {
        this.getQuotes(1);
    }
}
</script>