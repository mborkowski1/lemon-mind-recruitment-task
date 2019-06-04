<template>
    <v-container>
        <v-layout class="justify-center">
            <v-snackbar v-model="snackbar" multi-line :timeout=2000 top>
                Form has been sent
                <v-btn color="green" flat @click="snackbar = false">Close</v-btn>
            </v-snackbar>
            <v-flex class="xs12 sm8 md6 lg4">
                <v-form class="text-xs-center" @submit.prevent="submit">
                    <v-text-field v-model="transportFrom" :error-messages="transportFromErrors" type="text" label="Transport from" required @input="$v.transportFrom.$touch()" @blur="$v.transportFrom.$touch()"></v-text-field>
                    <v-text-field v-model="transportTo" :error-messages="transportToErrors" type="text" label="Transport to" required @input="$v.transportTo.$touch()" @blur="$v.transportTo.$touch()"></v-text-field>
                    <v-select v-model="plane" :error-messages="planeErrors" :items="availablePlanes" label="Plane type" offsetY required @input="$v.plane.$touch()" @blur="$v.plane.$touch()"></v-select>
                    <v-text-field v-model="transportDate" type="date" :error-messages="transportDateErrors" label="Transport date" required @input="$v.transportDate.$touch()" @blur="$v.transportDate.$touch()"></v-text-field>
                    <Cargo v-for="(cargo, index) in cargos" :key="index" :cargo="cargo" :plane-type="plane" @removeCargo="removeCargo" @changeName="changeName" @changeWeight="changeWeight" />
                    <div>
                        <v-btn class="primary" @click="addNewCargo">Add New Cargo</v-btn>
                    </div>
                    <v-btn class="success" :disabled="$v.$invalid" type="submit">Submit</v-btn>
                    <v-btn class="error" @click="reset">Reset</v-btn>
                </v-form>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
    import Cargo from '../components/Cargo'
    import { required } from 'vuelidate/lib/validators'

    const checkFutureDate = (value) => {
        return Date.parse(value) > Date.now()
    };

    const checkWeekdayDate = (value) => {
        const date = new Date(value);
        if (date.getDay() === 1 || date.getDay() === 2 || date.getDay() === 3 || date.getDay() === 4 || date.getDay() === 5)
            return 1;
        else
            return 0;
    };

    export default {
        name: 'Main',
        components: {
            Cargo
        },
        validations: {
            transportFrom: {
                required
            },
            transportTo: {
                required
            },
            plane: {
                required
            },
            transportDate: {
                required,
                checkFutureDate,
                checkWeekdayDate
            }
        },
        data () {
            return {
                transportFrom: '',
                transportTo: '',
                plane: null,
                transportDate: null,
                menu: false,
                snackbar: false,
                availablePlanes: ['Airbus A380', 'Boeing 747'],
                documents: [],
                cargos: [{id: 1, name: '', weight: null}]
            }
        },
        methods: {
            submit() {
                this.$v.touch();
                if (!this.$v.$invalid) {
                    this.snackbar = true;
                    this.reset()
                }
            },
            reset() {
                this.$v.$reset();
                this.plane = null;
                this.transportFrom = '';
                this.transportTo = '';
                this.transportDate = null;
                this.cargos = [{id: 1, name: '', weight: null}];
            },
            addNewCargo() {
                const newId = this.cargos[this.cargos.length - 1].id + 1;
                this.cargos.push({id: newId, name: '', weight: null})
            },
            removeCargo(id) {
                this.cargos = this.cargos.filter(cargo => {
                    return cargo.id !== id;
                })
            },
            changeName(object) {
                const cargoToUpdate = this.cargos.find(cargo => {
                    cargo.id = object.id;
                });
                const index = this.cargos.indexOf(cargoToUpdate);
                this.cargos[index].name = object.value;
            },
            changeWeight(object) {
                const cargoToUpdate = this.cargos.find(cargo => {
                    cargo.id = object.id;
                });
                const index = this.cargos.indexOf(cargoToUpdate);
                this.cargos[index].weight = object.value
            }
        },
        computed: {
            transportFromErrors() {
                const errors = [];
                if (!this.$v.transportFrom.$dirty) return errors;
                !this.$v.transportFrom.required && errors.push('Transport from is required');
                return errors
            },
            transportToErrors() {
                const errors = [];
                if (!this.$v.transportTo.$dirty) return errors;
                !this.$v.transportTo.required && errors.push('Transport to is required');
                return errors
            },
            planeErrors() {
                const errors = [];
                if (!this.$v.plane.$dirty) return errors;
                !this.$v.plane.required && errors.push('Plane type is required');
                return errors
            },
            transportDateErrors() {
                const errors = [];
                if (!this.$v.transportDate.$dirty) return errors;
                !this.$v.transportDate.required && errors.push('Transport date is required');
                !this.$v.transportDate.checkFutureDate && errors.push('Transport date must be future date');
                !this.$v.transportDate.checkWeekdayDate && errors.push('Transport date must contains weekday');
                return errors
            }
        }
    }
</script>

<style lang="scss" scoped>
    p {
        color: red;
    }
</style>
