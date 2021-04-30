<template>
    <div class="container">
        <div class="form-header">
            <h1 class="logo">Spinorama</h1>
        </div>
        <div class="user-picture">
            <img
                src="https://avatars.dicebear.com/api/male/jwnfdj23o4njh.svg"
                alt="obrázek uživatele"
            >
        </div>
        <form action="submit">
            <label for="form-name">Jméno</label>
            <input type="text"
                id="form-name" name="form-name"
                v-model="formData.firstname"
            >
            <label for="form-lastname">Příjmení</label>
            <input type="text"
                id="form-lastname" name="form-lastname"
                v-model="formData.lastname"
            >
            <label for="form-email">Email</label>
            <input type="text"
                id="form-email" name="form-email" placeholder="johndoe@mail.com"
                v-model="formData.email"
            >
            <div>
                <label v-if="activeEmailInputs > 0" for="form-email">Email {{ activeEmailInputs + 1 }}</label>
                <input v-if="activeEmailInputs > 0" type="text"
                    id="form-email" name="form-email"
                    v-model="formData.email1"
                >
            </div>
            <div class="add-email" @click.prevent="addEmailInput" v-if="activeEmailInputs < 3">
                <span class="add-email-button">+</span>
                <p class="add-email-text">Přidat email</p><br> 
            </div>
            <div class="save-div">
                <button class="save-btn" @submit.prevent="">Uložit</button>
            </div>
        </form>
    </div>
</template>


<script>
export default {
    data(){
        return{
            formData: {
                firstname: "",
                lastname: "",
                email: ""
            },
            activeEmailInputs: 0,
        }
    },
    methods: {
        addEmailInput(){
            if(this.activeEmailInputs > 3) return;
            this.activeEmailInputs++
        }
    }    
}
</script>


<style scoped>
    .container {
        align-content: center;
        margin: 50px auto;
        border: 1px solid hsla(0, 0%, 50%, 0.400);
        max-width: fit-content;
        color: hsl(200, 100%, 50%);
    }  

    .form-header {
        padding: 4em 4em;
        background: linear-gradient(to bottom right,
            hsl(190, 80%, 50%) 50%, transparent 50.5%)
            no-repeat bottom,
            linear-gradient(0deg, hsl(190, 80%, 50%), hsl(190, 80%, 50%))
            no-repeat top;
        background-size: 100% 7em, 100% calc(100% - 7em)
    }

    .logo {
        color: hsl(210, 70%, 30%);
        font-size: 3.3rem;
        font-weight: 800;
        margin: auto auto 2.5em auto;
        text-align: center;
    }

    .user-picture {
        position: relative;
        bottom: 120px;
        float: center;
        background-color: hsl(202, 100%, 96%);
        margin: 2em auto -8em auto;
        width: 100px;
        height: 100px;
    }

    .user-picture img {
        background-color: white;
        border-radius: 50%;
    }

    form {
        
        padding: 4em 4em;
    }

    label, p {
        font-size: 0.9rem;
        font-weight: bold;
    }

    input {
        background-color: hsl(202, 100%, 96%);
        color: hsl(200, 100%, 50%);
        height: 40px;
        width: 95%;
        padding-left: 1em;
        margin-top: 0.5em;
        margin-bottom: 1em;
        border-radius: 0.8em;
        border: 3px solid hsl(202, 100%, 85%);
        display: block;
    }

    input:focus {
        background-color: hsl(202, 100%, 93%);
        outline: none;
    }

    ::placeholder {
        color: hsl(200, 100%, 50%);
        font-size: 1.1em;
    }

    .add-email-text {
        display: inline-block;
        margin-left: 1em;
        font-size: 1em;
    }

    .add-email-button {
        display: inline-block;
        border: 3.2px solid hsl(200, 100%, 50%);
        border-radius: 50%;
        padding: 0 0.4rem;
        color: hsl(200, 100%, 50%);
        font-size: 1.3em;
    }

    .save-div {
        display: flex;
        justify-content: space-around;
    }

    .save-btn {
        background-color: hsl(200, 100%, 50%);
        padding: 2em 6em 1.2em 6em;
        margin-top: 4em;
        border-radius: 0.5rem;
        border: none;
        text-align: center;
        font-size: 0.7rem;
        color: white;
    }

    .save-btn:hover {
        cursor: pointer;
        background-color: hsl(200, 100%, 70%);
    }

    .add-email-button:hover, .add-email:hover {
        cursor: pointer;
        border-color: hsl(200, 100%, 70%);
        color: hsl(200, 100%, 70%);
    }

    @media(max-width: 470px){
        .logo {
            font-size: 2.2rem;    
        }

        .form-header {
            padding: 4em 0;
        }
    }
</style>