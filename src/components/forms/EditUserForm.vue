<template>
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
                v-model.trim="formData.name"
            >
            <label for="form-img">Avatar</label>
            <input type="text"
                id="form-img" name="form-img" placeholder="https://..."
                v-model.trim="formData.img"
            >
            <label for="form-email">Email</label>
            <input type="text"
                id="form-email" name="form-email"
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
            <p class="success center" v-if="messages.formUserAdded">Změny uloženy</p>
            <p class="center back" @click.prevent="$emit('hideForm')">zpátky na seznam</p>
        </form>
</template>

<script>
export default {
    data(){
        return{
            formData: {
                name: "",
                img: "",
                emails: [""]
            },
            activeEmailInputs: [],
            messages: {
                mailError: [
                    false,
                ],
                formError: false,
                formUserAdded: false,
            },
            user: {}
        }
    },
    props: {
        userID: Number,
        api: String
    },
    created(){
        this.getUser()
    },
    methods: {
        getUser(){
            fetch(this.api + "/" + this.userID)
            .then(res => res.json())
            .then(data => {
                this.user = data.data[0];
                console.log(data.data[0]);
                this.formData.name = this.user.name;
                this.formData.img = this.user.avatar;
                this.formData.emails = this.user.emails.slice();
            })
        },
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
            data.emails.splice(0, data.emails.length, "");
            this.activeEmailInputs.slice(0, this.activeEmailInputs.length)
        },
        createUser(){
            let fullname = this.formData.firstname;
            if (!this.isInputEmpty(this.formData.lastname)){
                fullname += " " + this.formData.lastname
            }

            let newUser = {
                name: fullname,
                emails: this.formData.emails.slice(),
                data: null,
                avatar: null
            };
            return JSON.stringify(newUser)
        },
        addUser(){
            const jsonData = this.createUser();

            fetch(this.api, {
                method: 'POST',
                headers: {
                    "Content-Type": "application/json",
                    "Accept": "application/json"
                },
                body: jsonData
            })
            .then(response => {
                if(response.ok) {
                    this.userAdded(jsonData)
                }
            })
            .catch(error => {
                console.log(error);
                this.messages.formError = true; 
            })
        },
        userAdded(user){
            console.log("Added user: " + user)
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
    @import '../../assets/form.css'
</style>