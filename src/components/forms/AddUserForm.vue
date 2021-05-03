<template>
    <div>
        <h3>Přidat uživatele</h3>

        <form action="submit">
            <label for="form-name">Jméno</label>
            <input type="text"
                id="form-name" name="form-name"
                v-model.trim="formData.firstname"
            >
            <p class="error" v-if="messages.nameError">Jméno je povinné</p>

            <label for="form-lastname">Příjmení</label>
            <input type="text"
                id="form-lastname" name="form-lastname"
                v-model.trim="formData.lastname"
            >

            <label for="form-avatar">Avatar</label>
            <input type="text"
                id="form-avatar" name="form-avatar" placeholder="https://..."
                v-model.trim="formData.avatar"
            >

            <label for="form-email">Email</label>
            <input type="text"
                id="form-email" name="form-email" placeholder="johndoe@mail.com"
                v-model.trim="formData.emails[0]"
            >
            <p class="error" v-if="messages.mailError[0]">Neplatný email</p>

            <div v-for="num in activeEmailInputs" :key="num">
                <label for="form-email" v-if="haveMoreEmails">
                    Email {{ num + 1}}
                </label>
                <span class="delete" @click.prevent="removeEmailInput(num)">&#10006;</span>
                <input type="text"
                    v-if="haveMoreEmails"
                    id="form-email" name="form-email"
                    v-model.trim="formData.emails[num]"
                >
                <p class="error" v-if="messages.mailError[num]">Neplatný email</p>
            </div>

            <div class="add-email" @click.prevent="addEmailInput" v-if="activeEmailInputs.length < 3">
                <span class="add-email-button">+</span>
                <p class="add-email-text">Přidat email</p><br> 
            </div>
            
            <p class="error" v-if="messages.formError">Někde se stala chyba. Zkus to znovu.</p>
            <div class="save-div">
                <button class="save-btn" @click.prevent="validateForm">Uložit</button>
            </div>
            <p class="success center" v-if="messages.formUserAdded">Uživatel uložen</p>
            <p class="center back" @click.prevent="$emit('hideForm')">zpátky na seznam</p>
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
                avatar: "",
                emails: [""]
            },
            activeEmailInputs: [],
            messages: {
                nameError: false,
                mailError: [
                    false,
                ],
                formError: false,
                formUserAdded: false,
            },
        }
    },
    props: {
        api: String,
        headers: Object
    },
     emits: [
        'hideForm'
    ],
    methods: {
        addEmailInput(){
            this.activeEmailInputs.push(this.activeEmailInputs.length + 1);
            this.formData.emails.push("");
            this.messages.mailError.push(false);
        },
        removeEmailInput(num){
            this.activeEmailInputs.splice(this.activeEmailInputs.indexOf(num), 1);
            this.formData.emails.splice(num, 1);
            this.messages.mailError.splice(num, 1);
        },
        isInputEmpty(input){
            if (input.length > 0) return false;
            return true;
        },
        isEmailValid(email){
            if (this.isInputEmpty(email)) return false;
            if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) return false;
            return true;
        },
        validateEmails(){
            let results = [];
            let emails = this.formData.emails;

            for(let email in emails){
                let emailValid = this.isEmailValid(emails[email]);
                if(emailValid) {
                    results.push(true)
                }
                else if (!emailValid) {
                    this.messages.mailError[email] = true;
                    results.push(false);
                }    
            }
            return results.every(result => result === true)
        },
        validateForm(){
            this.hideMessages();
            let nameValid = !this.isInputEmpty(this.formData.firstname);
            let emailsValid = this.validateEmails();

            if (nameValid && emailsValid) {
                this.addUser();
                return
            }
            if (!nameValid) {
                this.messages.nameError = true;
            }            
        },
        hideMessages(){
            let text = this.messages;
            text.nameError = false;
            text.mailError.splice(0, text.mailError.length, false)
            text.formError = false;
            text.formUserAdded = false;
        },
        clearForm(){
            let data = this.formData;            
            data.firstname = "";
            data.lastname = "";
            data.avatar = "";
            data.emails.splice(0, data.emails.length, "");
            this.activeEmailInputs.slice(0, this.activeEmailInputs.length)
        },
        createUser(){
            let fullname = (this.formData.firstname + " " + this.formData.lastname).trim();
            let avatar = null;
            if (!this.isInputEmpty(this.formData.avatar)) {avatar = this.formData.avatar}

            let newUser = {
                name: fullname,
                emails: this.formData.emails.slice(),
                data: null,
                avatar: avatar
            };
            return JSON.stringify(newUser)
        },
        addUser(){
            const jsonData = this.createUser();

            fetch(this.api, {
                method: 'POST',
                headers: this.headers,
                body: jsonData
            })
            .then(response => {
                if(response.ok) {
                    this.userAdded()
                }
            })
            .catch(error => {
                console.log(error);
                this.messages.formError = true; 
            })
        },
        userAdded(){
            console.log("User added.")
            this.messages.formUserAdded = true;
            this.clearForm();
            setTimeout(()=>{ this.messages.formUserAdded = false }, 5000)
        },      
    },
    computed: {
        haveMoreEmails(){
            if (this.activeEmailInputs.length > 0) return true;
            return false;
        }
    }    
}
</script>

<style scoped>
    @import '../../assets/form.css';
</style>
