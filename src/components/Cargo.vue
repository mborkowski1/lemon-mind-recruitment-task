<template>
    <div>
        <v-text-field ref="cargoName" :disabled="planeType === null" :error-messages="nameErrors" @input="$v.name.$touch()" @blur="$v.name.$touch()" v-model="name" label="Cargo name" required></v-text-field>
        <v-text-field ref="cargoWeight" :disabled="planeType === null" :error-messages="weightErrors" @input="$v.weight.$touch()" @blur="$v.weight.$touch()" v-model="weight" type="number" label="Cargo weight (kg)" required></v-text-field>
        <p v-show="planeType === null">Plane type must be chosen</p>
        <v-btn v-show="cargo.id !== 1" class="warning" @click="removeCargo(cargo.id)">Delete Cargo</v-btn>
    </div>
</template>

<script>
    import { required, minValue } from 'vuelidate/lib/validators'

    const maxWeightAirbus = (value, vm) => {
        if (vm.plane === 'Airbus A380' && value > 35000)
            return 1;
        else
            return 0;
    };

    const maxWeightBoeing = (value, vm) => {
        if (vm.plane === 'Boeing 747' && value > 38000)
            return 1;
        else
            return 0;
    };

    export default {
        name: "Cargo",
        props: {
            cargo: Object,
            planeType: String
        },
        validations: {
            name: {
                required
            },
            weight: {
                required,
                minValue: minValue(1),
                maxWeightAirbus,
                maxWeightBoeing
            }
        },
        data () {
            return {
                name: this.cargo.name,
                weight: this.cargo.weight,
                plane: this.planeType
            }
        },
        methods: {
            removeCargo(id) {
                this.$emit('removeCargo', id)
            }
        },
        updated() {
            this.name = this.cargo.name;
            this.weight = this.cargo.weight;
        },
        computed: {
            nameErrors() {
                const errors = [];
                if (!this.$v.name.$dirty) return errors;
                !this.$v.name.required && errors.push('Name is required');
                return errors
            },
            weightErrors() {
                const errors = [];
                if (!this.$v.weight.$dirty) return errors;
                !this.$v.weight.required && errors.push('Weight is required');
                !this.$v.weight.minValue && errors.push('Weight must be greater than 0');
                if (this.$v.weight.maxWeightAirbus) errors.push('Weight cannot be greater than 35000');
                else if (this.$v.weight.maxWeightBoeing) errors.push('Weight cannot be greater than 38000');
                return errors
            }
        },
        watch: {
            name(value) {
                this.cargo.name = value
            },
            weight(value) {
                this.cargo.weight = value
            },
            planeType(value) {
                this.plane = value
            }
        }
    }
</script>

<style lang="scss" scoped>
    p {
        color: red;
    }
</style>
