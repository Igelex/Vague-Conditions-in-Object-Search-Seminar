<template>
    <div class=" text-xs-center signup-form-container d-flex column wrap justify-center align-center"
         :class="toggleLogin">
        <div>
            <h1>Add New User</h1>
            <p class="mb-5 mt-3">Enter your name and password</p>
            <label class="mt-5" for="name">
                Name
                <input type="text" id="name" v-model="name">
            </label>
            <label for="pass">
                Password
                <input type="text" id="pass" v-model="password">
            </label>
            <btn-prim class="mt-5" @click.native="signup">
                sing up
            </btn-prim>
        </div>
    </div>
</template>

<script>
    import BtnPrim from "../Base/BtnPrim";
    export default {
        name: "SignupForm",
        components: {BtnPrim},
        props: ['active'],
        data: () => ({
            name: 'test',
            password: 'test',
        }),
        computed: {
            toggleLogin() {
                if (!this.active) {
                    return {
                        'hidden': true
                    };
                }
                return {
                    'active': true
                };

            }
        },
        methods: {
            signup() {
                this.$emit('signup', {name: this.name, password: this.password})
            }
        }
    }
</script>

<style scoped lang="scss">
    .signup-form-container {
        padding: 10%;
        height: 100%;
        width: 60%;
        position: absolute;
        top: 0;
        left: 0;
        background-color: white;

        &.active {
            animation: show-form 1.3s ease forwards;
        }

        &.hidden {
            animation: hide-form 1.3s ease forwards;
        }

        h1 {
            font-weight: bold;
            font-size: 2.3rem;
            letter-spacing: 0.3rem;
        }

        p {
            font-weight: lighter;
            font-size: 1.5rem;
            letter-spacing: 0.1rem;
        }

        label {
            text-align: left !important;
            text-transform: uppercase;
            letter-spacing: 0.2rem;
            display: block;
            width: 100%;
            color: black;

            input {
                transition: all 0.3s;
                font-weight: bold;
                font-size: 1.2rem;
                padding-left: 10px;
                outline: none;
                color: black;
                margin: 10px 0;
                background-color: #f4f8f7;
                height: 50px;
                border-radius: 3px;
                width: 100%;

                &:focus {
                    border-bottom: 3px solid deepskyblue;
                }
            }
        }
    }
    @keyframes hide-form {
        0% {
            opacity: 1;
            left: 0;
        }
        50% {
            opacity: 0;
        }
        100% {
            z-index: 0;
            opacity: 0;
            left: 20%;
        }
    }
    @keyframes show-form {
        0% {
            opacity: 0;
            left: 10%;
        }
        50% {
            opacity: 1;
        }
        100% {
            z-index: 1;
            opacity: 1;
            left: 0;
        }
    }
</style>