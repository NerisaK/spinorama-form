<template>
    <div class="picture" v-if="user.avatar">
            <img
                :src="user.avatar"
                alt="obrázek uživatele"
            >
    </div>

    <h3>Upravit uživatele</h3>

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
            <button class="save-btn" @click.prevent="saveChanges">Uložit</button>
        </div>
        <p class="success center" v-if="messages.formUserChanged">Změny uloženy</p>
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
                formUserChanged: false,
            },
            user: {}
        }
    },
    props: {
        userID: Number,
        api: String
    },
    emits: [
        'hideForm'
    ],
    created(){
        this.getUser()
    },
    methods: {
        getUser(){
            fetch(this.api + "/" + this.userID)
            .then(res => res.json())
            .then(data => {
                this.user = data.data[0];
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
        isInputEmpty(input){
            if(input === null) return true;
            if (input.length > 0) return false;
            return true;
        },        
        stageChanges(){
            let user = this.user;
            let data = this.formData;
            let changes = user;

            if(!this.isInputEmpty(data.name)){
                changes.name = data.name      
            }
            
            if(!this.isInputEmpty(data.img)){
                changes.avatar = data.img       
            }

            if(!this.isInputEmpty(data.emails[0])){
                changes.emails = data.emails.slice()      
            }
            else if(this.isInputEmpty(data.emails[0])){
                let emailsArr = [];
                emailsArr.push(user.emails[0]);
                emailsArr.push(data.emails.slice(1))
            }
            
            return JSON.stringify(changes)            
        },
        changesValid(){
            this.hideMessages();
            if (!this.validateEmails()) return false;

            let user = this.user;
            let data = this.formData;

            if((user.name === data.name)
                && (user.avatar === data.img)
                && (user.emails === data.emails)
            ) return false;
            
            return true;
        },
        saveChanges(){
            if(!this.changesValid) return;

            let jsonData = this.stageChanges();
            
            fetch(this.api + "/" + this.userID, {
                method: 'PATCH',
                headers: {
                    "Content-Type": "application/json",
                    "Accept": "application/json"
                },
                body: jsonData
            })
            .then(response => {
                if(response.ok) {
                    this.userChanged()
                }
            })
            .catch(error => {
                console.log(error);
                this.messages.formError = true; 
            })
        },
        userChanged(){
            console.log("User data was successfully changed")
            this.messages.formUserChanged = true;
            setTimeout(()=>{
                this.messages.formUserChanged = false;
                this.getUser();
            }, 3000)
        },
        hideMessages(){
            let text = this.messages;
            text.mailError.splice(0, text.mailError.length, false)
            text.formError = false;
            text.formUserChanged = false;
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
    
    .picture {
        background-color: hsl(202, 100%, 96%);
    }
</style>