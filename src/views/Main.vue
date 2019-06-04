<template>
    <v-container>
        <v-layout class="justify-center">
            <v-snackbar v-model="snackbar" multi-line :timeout=2000 top>
                Form has been sent
                <v-btn color="green" flat @click="snackbar = false">Close</v-btn>
            </v-snackbar>
            <v-flex class="xs12 sm8 md6 lg4">
                <v-form ref="form" lazy-validation v-model="valid" class="text-xs-center">
                    <v-text-field v-model="transportFrom" :rules="transportFromRules" label="Transport from" required></v-text-field>
                    <v-text-field v-model="transportTo" :rules="transportToRules" label="Transport to" required></v-text-field>
                    <v-select v-model="plane" :rules="planeRules" :items="availablePlanes" label="Plane type" offsetY required></v-select>
                    <v-text-field v-model="transportDate" type="date" :rules="transportDateRules" :error-messages="transportDateErrors()" label="Transport date" required></v-text-field>
                    <Cargo v-for="(cargo, index) in cargos" :key="index" :cargo="cargo" :plane-type="plane" @removeCargo="removeCargo" />
                    <div>
                        <v-btn class="primary" @click="addNewCargo">Add New Cargo</v-btn>
                    </div>
                    <v-btn class="success" :disabled="!valid" @click="submit">Submit</v-btn>
                    <v-btn class="error" @click="reset">Reset Form</v-btn>
                </v-form>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
    import Cargo from '../components/Cargo'

    export default {
        name: 'Main',
        components: {
            Cargo
        },
        data () {
            return {
                valid: false,
                transportFrom: '',
                transportFromRules: [
                    v => !!v || 'Transport from is required'
                ],
                transportTo: '',
                transportToRules: [
                    v => !!v || 'Transport to is required'
                ],
                plane: null,
                planeRules: [
                    v => !!v || 'Plane type is required'
                ],
                transportDate: null,
                transportDateRules: [
                    v => !!v || 'Transport date is required',
                    v => (v && Date.parse(this.transportDate) > Date.now()) || 'Transport date must be future date'
                ],
                menu: false,
                availablePlanes: ['Airbus A380', 'Boeing 747'],
                documents: [],
                snackbar: false,
                cargos: [{id: 1, name: '', weight: null}]
            }
        },
        methods: {
            submit() {
                if (this.$refs.form.validate()) {
                    this.snackbar = true;
                    this.reset();
                }
            },
            reset() {
                this.$refs.form.reset();
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
            transportDateErrors() {
                const date = new Date(this.transportDate);
                if (!(date.getDay() === 1 || date.getDay() === 2 || date.getDay() === 3 || date.getDay() === 4 || date.getDay() === 5)) return 'Transport date must contains weekday';
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>
