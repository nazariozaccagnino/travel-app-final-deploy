<template>
    <div class="text-center">
        <h1>Con Travel Memories puoi facilmente tener traccia dei tuoi viaggi e delle tue tappe.</h1>
        <h2>Clicca su "aggiungi nuovo viaggio" per iniziare</h2>
    </div>
    <div class="d-flex justify-content-center">
        <div class="btn btn-primary mx-2 my-2" @click="openModal">+ Aggiungi nuovo viaggio</div>
    </div>
    <div class="text-center" v-if="store.travels.length > 0">
        <h6>Scorri a destra e sinistra per visualizzare tutte le tappe</h6>
    </div>

    <div class="mx-4 my-4">
        <div v-for="(item, index) in store.travels" :key="index"
            class="row row-cols-auto my-4 flex-nowrap overflow-x-scroll">
            <div class="card h-100" style="width: 300px;">
                <div class="card-body">
                    <div>
                        <h3>Viaggio a:</h3>
                        <h5 class="card-title font-weight-bold mb-2">{{ item.destination }}</h5>
                        <p class="card-text"><i class="far fa-clock pe-2"></i>{{ item.startdate }} -> {{
                            item.enddate }}</p>
                    </div>
                </div>
                <div class="bg-image hover-overlay ripple rounded-0" data-mdb-ripple-color="light">
                    <img class="cardimg" :src=getTripImg(item) id="tripimg" alt="trip-img" />
                    <a href="#!">
                        <div class="mask" style="background-color: rgba(251, 251, 251, 0.15);"></div>
                    </a>
                </div>
                <div class="card-body">
                    <p class="card-text">{{ item.description }}</p>
                    <p class="card-text"><span v-html="tripStars(item)"></span></p>
                    <div class="d-flex justify-content-between">
                        <button type="button" class="btn btn-success btn-sm mx-2"
                            @click="openModal2(index, item)">Aggiungi
                            nuova tappa</button>
                        <button type="button" class="btn btn-danger btn-sm mx-2" @click="deleteTrip(index)">Elimina
                            viaggio</button>
                    </div>
                </div>
            </div>

            <div v-for="(childItems, ind) in item.details" :key="index" class="col">
                <div class="card mb-2 h-100" style="width: 300px;">
                    <div class="card-body">
                        <h3>Tappa a:</h3>
                        <h5 class="card-title">{{ childItems.name }}</h5>
                        <div>
                            <p class="card-text"><i class="far fa-clock pe-2"></i>{{ childItems.startdate }}
                                -> {{
                                    childItems.enddate }}</p>
                        </div>
                        <div class="bg-image hover-overlay ripple rounded-0" data-mdb-ripple-color="light">
                            <img class="cardimg" :src=getLegImg(childItems) alt="Card image cap" />
                            <a href="#!">
                                <div class="mask" style="background-color: rgba(251, 251, 251, 0.15);"></div>
                            </a>
                        </div>
                        <p class="card-text">{{ childItems.notes }}</p>
                        <p class="card-text"><span v-html="legStars(childItems)"></span></p>
                        <button type="button" class="btn btn-sm btn-danger" @click="deleteLeg(index, ind)">Elimina
                            Tappa</button>
                    </div>
                </div>
            </div>
        </div>
        <div class="d-flex justify-content-center">
            <div class="btn btn-danger mx-2" @click="emptyTravels">Elimina tutto</div>
        </div>
    </div>
    <!--modale-->
    <div v-if="showModal" class="modal fade show d-block" id="exampleModal" tabindex="-1"
        aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="exampleModalLabel">Inserisci nuovo viaggio</h1>
                </div>
                <div class="modal-body">
                    <form>
                        <div class="mb-3">
                            <div id="map"></div>
                            <div>Inserisci nome destinazione</div>
                            <input type="text" class="form-control" v-model="newTrip.destination">
                            <div>Inserisci data inizio viaggio</div>
                            <input type="date" class="form-control" v-model="newTrip.startdate">
                            <div>Inserisci data fine viaggio</div>
                            <input type="date" class="form-control" v-model="newTrip.enddate">
                            <div class="mb-3">
                                <label for="formFile" class="form-label">Inserisci foto</label>
                                <input class="form-control" type="file" id="formFile" @change="tripImgAdd">
                            </div>
                            <div>Inserisci descrizione sintetica</div>
                            <input type="textarea" class="form-control" v-model="newTrip.description">
                            <div>Inserisci valutazione</div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="1" v-model="newTrip.rating">
                                <label class="form-check-label" for="inlineRadio1">1 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="2" v-model="newTrip.rating">
                                <label class="form-check-label" for="inlineRadio1">2 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="3" v-model="newTrip.rating">
                                <label class="form-check-label" for="inlineRadio1">3 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="4" v-model="newTrip.rating">
                                <label class="form-check-label" for="inlineRadio1">4 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="5" v-model="newTrip.rating">
                                <label class="form-check-label" for="inlineRadio1">5 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="closeModal">Close</button>
                    <button type="button" class="btn btn-primary" @click="addTrip(this.newTrip)" id="savebtn">Save
                        changes</button>
                </div>
            </div>
        </div>
    </div>
    <!--modale2-->
    <div v-if="showModal2" class="modal fade show d-block" id="exampleModal" tabindex="-1"
        aria-labelledby="exampleModalLabel">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="exampleModalLabel">Inserisci nuovo viaggio</h1>
                </div>
                <div class="modal-body">
                    <form>
                        <div class="mb-3">
                            <div v-if="this.error == true">ERROREE</div>
                            <div id="map"></div>
                            <div>Inserisci nome tappa</div>
                            <input type="text" class="form-control" v-model="newLeg.name">
                            <div>Inserisci data arrivo</div>
                            <input type="date" class="form-control" v-model="newLeg.startdate">
                            <div>Inserisci data partenza</div>
                            <input type="date" class="form-control" v-model="newLeg.enddate">
                            <div class="mb-3">
                                <label for="formFile" class="form-label">Inserisci foto</label>
                                <input class="form-control" type="file" id="formFile" @change="legImgAdd">
                            </div>
                            <div>Inserisci note</div>
                            <input type="textarea" class="form-control" v-model="newLeg.notes">
                            <div>Inserisci valutazione</div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="1" v-model="newLeg.rating">
                                <label class="form-check-label" for="inlineRadio1">1 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="2" v-model="newLeg.rating">
                                <label class="form-check-label" for="inlineRadio1">2 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="3" v-model="newLeg.rating">
                                <label class="form-check-label" for="inlineRadio1">3 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="4" v-model="newLeg.rating">
                                <label class="form-check-label" for="inlineRadio1">4 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                            <div class="form-check form-check-inline">
                                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                                    value="5" v-model="newLeg.rating">
                                <label class="form-check-label" for="inlineRadio1">5 <i class="fa-solid fa-star"
                                        style="color: #FFD43B;"></i></label>
                            </div>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="closeModal">Close</button>
                    <button type="button" class="btn btn-primary" @click="addLeg(this.newLeg, index)" id="savebtn">Save
                        changes</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { store } from '../store';
export default {
    name: 'MainComponent',
    data() {
        return {
            store,
            error: false,
            showModal: false,
            showModal2: false,
            i: 0,
            address: {},
            newTrip: {
                destination: '',
                startdate: '',
                enddate: '',
                description: '',
                rating: '',
                img: '',
                details: []
            },
            newLeg: {
                name: '',
                startdate: '',
                enddate: '',
                place: {
                    latitude: 0,
                    longitude: 0
                },
                img: '',
                notes: '',
                rating: '',
            },
            errors: {
                destination: '',
                startdate: '',
                enddate: '',
                description: '',
                rating: ''
            },
        }
    },
    methods: {
        addTrip(newTrip) {
            // if (this.newTrip.destination === '') {
            //     const input = document.getElementById(savebtn);
            //     this.error = true;
            // }
            // else {
            this.errorInputName = false;
            this.store.travels.push({ ...newTrip })
            this.newTrip.destination = '';
            this.newTrip.startdate = '';
            this.newTrip.enddate = '';
            this.newTrip.description = '';
            this.newTrip.rating = '';
            this.newTrip.img = '';
            this.newTrip.details = [];
            console.log(this.store.travels, 'poirurgjh');
            this.saveTravels();
            this.closeModal();
            // }
        },
        bottone() {
            this.loadTravels()
        },
        addLeg(newLeg, index) {
            this.errorInputName = false;
            console.log(index);
            console.log(this.newLeg);
            this.store.travels[this.i].details.push({ ...newLeg });
            this.i = 0;
            this.newLeg.name = '';
            this.startdate = '',
                this.enddate = '',
                this.newLeg.place = {
                    "latitude": 0,
                    "longitude": 0,
                },
                this.newLeg.img = '';
            this.newLeg.notes = '';
            this.saveTravels();
            this.closeModal();
        },
        openModal() {
            this.showModal = true;
            let showmap = setTimeout(() => {
                this.map();
            }, 1000);
        },
        openModal2(index, item) {
            this.showModal2 = true;
            this.i = index
            let showmap = setTimeout(() => {
                this.map();
            }, 1000);
        },
        closeModal() {
            this.showModal = false;
            this.showModal2 = false;
        },
        validateForm() {
            let isValid = true;
            // Name
            if (this.newTrip.destination.trim() === '') {
                this.errors.name = 'La destinazione è obbligatoria';
                isValid = false;
            }
            return isValid
        },
        saveTravels() {
            localStorage.setItem('travels', JSON.stringify(this.store.travels));
        },
        loadTravels() {
            const savedTravels = localStorage.getItem('travels');
            if (savedTravels) {
                this.store.travels = JSON.parse(savedTravels);
            }
            console.log(this.store.travels);
        },
        emptyTravels() {
            this.store.travels = [];
            localStorage.clear();
        },
        map() {
            let mapOptions = {
                center: [51.5073219, -0.1276474],
                zoom: 15
            }

            let map = new L.map('map', mapOptions);


            let layer = new L.TileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png');
            map.addLayer(layer);
            let apiKey = "9098219d18994560be55415be86df062",
                marker = null;


            const addressSearchControl = L.control.addressSearch(apiKey, {
                position: 'topright',

                //set it true to search addresses nearby first
                mapViewBias: true,

                //Text shown in the Address Search field when it's empty
                placeholder: "Enter an address here",

                // /Callback to notify when a user has selected an address
                resultCallback: (address) => {
                    this.address = address
                    this.newTrip.destination = address.address_line1
                    this.newLeg.name = address.address_line1
                    this.address = '';
                    // If there is already a marker remove it
                    if (marker) {
                        marker.remove();
                    }
                    //Prevent throwing Errors when the address search box is empty
                    if (!address) {
                        return;
                    }

                    //add marker 
                    marker = L.marker([address.lat, address.lon]).addTo(map);
                    //Sets the view of the map (geographical center and zoom) with the given animation options.
                    map.setView([address.lat, address.lon], 20);
                },


                //Callback to notify when new suggestions have been obtained for the entered text
                suggestionsCallback: (suggestions) => {
                    // console.log(suggestions);
                }
            });


            map.addControl(addressSearchControl);
        },
        tripImgAdd(event) {
            console.log(event.target.files);
            const file = event.target.files[0];
            // Gets file from input element
            const fr = new FileReader();
            // Creates new FileReader object
            fr.readAsDataURL(file);
            // Set FileReader to output data as URL string
            fr.addEventListener('load', () => {
                // Waits for file reading to be complete
                const url = fr.result
                // Save result
                const img = new Image();
                img.src = url;
                this.newTrip.img = url

                // let tripimg = document.getElementById('tripimg');
                // tripimg.appendChild(img)
                // Make URL src of image and append to DOM
            });
        },
        legImgAdd(event) {
            const file = event.target.files[0];
            // Gets file from input element
            const fr = new FileReader();
            // Creates new FileReader object
            fr.readAsDataURL(file);
            // Set FileReader to output data as URL string
            fr.addEventListener('load', () => {
                // Waits for file reading to be complete
                const url = fr.result
                // Save result
                const img = new Image();
                img.src = url;
                this.newLeg.img = url

                // let tripimg = document.getElementById('tripimg');
                // tripimg.appendChild(img)
                // Make URL src of image and append to DOM
            });
        },
        getTripImg(item) {
            return item.img;
        },
        getLegImg(item) {
            return item.img;
        },
        deleteTrip(index) {
            this.store.travels.splice(index, 1);
            // this.saveTravels();
        },
        deleteLeg(index, ind) {
            // this.store.travels[index].details[ind].splice(ind, 1);
            // this.saveTravels();
            console.log(index, 'padre');
            console.log(ind);
            this.store.travels[index].details.splice(ind, 1)
            this.saveTravels()
        },
        changeDate() {
            const oldDate = "2022-12-03";
            const newDate = new Date(oldDate);
            console.log(newDate.toLocaleDateString("it-IT"));
            console.log(newDate.getDate() + "-" + (newDate.getMonth() + 1) + "-" + newDate.getFullYear());
        },
        tripStars(item) {
            let str = '';
            let stars = `<i class="fa-solid fa-star" style="color: #FFD43B;"></i>`;
            str = stars.repeat(item.rating)
                return str
        },
        legStars(childItems){
            let str = '';
            let stars = `<i class="fa-solid fa-star" style="color: #FFD43B;"></i>`;
            str = stars.repeat(childItems.rating)
                return str
        },
    },
    mounted() {
        this.loadTravels();
        this.changeDate()
    }

}
</script>

<style lang="scss" scoped>
.modal.fade.show {
    display: block;
    background-color: rgba(0, 0, 0, 0.5);
}

/*card style*/
.card {
    border: 3px solid #0077b6;
    background-image: url('/public/mappamondo.png');
    background-color: #caf0f8;
    background-size: 50%;
    background-position: bottom right;
    background-repeat: no-repeat;

}

.riga {
    overflow-x: auto;
    white-space: nowrap;
    float: none;
}

#map {
    width: 100%;
    height: 250px;
}

.cardimg {
    max-width: 100%;
    max-height: 200px;
}

.riga {
    overflow-x: scroll;
}

*::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
    background-color: #F5F5F5;
    border-radius: 10px;
}

*::-webkit-scrollbar {
    width: 6px;
    height: 10px;
    background-color: #F5F5F5;
}

*::-webkit-scrollbar-thumb {
    border-radius: 10px;
    background-image: -webkit-gradient(linear,
            left bottom,
            left top,
            color-stop(0.44, rgb(122, 153, 217)),
            color-stop(0.72, rgb(73, 125, 189)),
            color-stop(0.86, rgb(28, 58, 148)));
}
</style>