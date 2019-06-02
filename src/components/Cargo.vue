<template>
    <div>
        <v-text-field :disabled="planeType === null" v-model="cargo.name" :rules="nameRules" label="Cargo name" required></v-text-field>
        <v-text-field :disabled="planeType === null" v-model="cargo.weight" type="number" :rules="weightRules" label="Cargo weight (kg)" required></v-text-field>
        <p v-show="planeType === null">Plane type must be chosen</p>
        <v-btn v-show="cargo.id !== 1" class="warning" @click="removeCargo(cargo)">Delete Cargo</v-btn>
    </div>
</template>

<script>
    export default {
        name: "Cargo",
        props: {
            cargo: Object,
            planeType: String
        },
        data () {
            return {
                nameRules: [
                    v => !!v || 'Name is required'
                ],
                weightRules: [
                    v => !!v || 'Weight is required',
                    v => (v && v > 1) || 'Weight must be greater than 0',
                    v => (v && !this.airbusWeightError) || 'Weight cannot be greater than 35000',
                    v => (v && !this.boeingWeightError) || 'Weight cannot be greater than 38000'
                ],
                airbusWeightError: false,
                boeingWeightError: false
            }
        },
        updated() {
            this.airbusWeightError = this.planeType === 'Airbus A380' && this.cargo.weight > 35000;
            this.boeingWeightError = this.planeType === 'Boeing 747' && this.cargo.weight > 38000;
        },
        methods: {
            removeCargo(cargo) {
                this.$emit('removeCargo', cargo.id)
            }
        }
    }
</script>

<style lang="scss" scoped>
    p {
        color: red;
    }
</style>
