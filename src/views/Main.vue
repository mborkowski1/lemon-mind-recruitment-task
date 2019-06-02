<template>
    <v-container>
        <v-layout class="justify-center">
            <v-flex class="xs12 sm8 md6 lg4">
                <v-form ref="form" lazy-validation v-model="valid" class="text-xs-center">
                    <v-text-field v-model="transportFrom" :rules="transportFromRules" label="Transport from" required></v-text-field>
                    <v-text-field v-model="transportTo" :rules="transportToRules" label="Transport to" required></v-text-field>
                    <v-select v-model="plane" :rules="planeRules" :items="availablePlanes" label="Plane type" offsetY required></v-select>
                    <v-menu v-model="menu" :close-on-content-click="false" lazy transition="scale-transition" offsetY full-width min-width="200px">
                        <template slot="activator">
                            <v-text-field v-model="transportDate" label="Transport date" prepend-icon="event" readonly></v-text-field>
                        </template>
                        <v-date-picker v-model="transportDate" @input="menu = false"></v-date-picker>
                    </v-menu>
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
                menu: false,
                availablePlanes: ['Airbus A380', 'Boeing 747'],
                documents: [],
                cargos: [{id: 1, name: '', weight: null}]
            }
        },
        methods: {
            submit() {
                if (this.$refs.form.validate()) {
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
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>
