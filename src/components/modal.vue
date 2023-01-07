<template>
    <div>
        <b-modal id="modal" class="mymodal" hide-header-close centered hide-footer has-modal-card size="xl">
            <template #modal-title>
                <div id="title">
                    <div id="div-title">
                        <h5>Links de redirecionamento <b-icon icon="globe2" variant="secundary"></b-icon></h5>
                        <p id="subtitle">Crie seus links de redirect em poucos passos</p>
                    </div>
                    <div id="buttons">
                        <button id="X" @click="$bvModal.hide('modal')">
                            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path d="M16.7844 1.30859L1.78442 16.3086M1.78442 1.30859L16.7844 16.3086" stroke="#B5B9C5" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                            </svg>
                        </button>
                        <br>
                        <button id="criar" @click="$bvModal.hide('modal'), createNew()" v-b-toggle.sidebar-1>Criar um link</button>
                    </div>
                </div>                 
            </template>
            <div id="main">
                <div id="redirect">
                    <div id="red-info">
                        <p id="number-links">{{ redirects.length - 1 }} links</p>
                        <p id="click-p">Cliques em tempo real</p>    
                    </div>
                    <div class="redirects" >
                        <div class="red-links" v-for="redirect in redirects" :key="redirect.id" :redirect="redirect" v-if=" redirect.name != 'novo link'">
                            <div @click="select(redirect)">
                                <div class="name-date">
                                    <p class="name">{{ redirect.name }}</p>
                                    <p class="date">{{ getDate(redirect.created_at) }}</p>
                                </div>
                                <div class="url-click">
                                    <p class="url">{{ redirect.hash }}</p>
                                    <p class="cliques">&#128073 000/{{redirect.total_limit}}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div id="links">
                    <div id="links-info">
                        <div id="name-date-links">
                            <p id="name-links">{{selected.name}}</p>
                            <p id="date-links" v-if="selected.name">Criado em {{getDate(selected.created_at)}}</p>
                        </div>
                        <div id="url-buttons-links">
                            <p id="url-links">{{ selected.hash }}</p>
                            <b-button id="copiar-links" variant="outline-primary" size="sm" v-if="selected.name" @click="copy(selected.hash)">Copiar</b-button>
                            <b-button id="editar-links" variant="outline-primary" size="sm" v-if="selected.name" v-b-modal.modal-2 @click="changeRedirect()">Editar</b-button>
                            <b-button id="deletar-links" variant="outline-danger" size="sm" v-if="selected.name" @click="deleteRedirect()">Deletar</b-button>
                        </div>
                    </div>
                    <div id="links-links">
                        <div id="false-table-links" v-for="link, index in links" :key="link.id" :link="link" v-if="selected.id == link.id_redirect">
                            <p id="id-links">{{ index + 1 }}</p>
                            <div id="url-limit">
                                <p id="url-link">{{ link.link }}</p>
                                <p id="limit-links">00/ {{ link.limit }}</p>    
                            </div>
                            <div id="only-for-margin">
                                <b-button id="editar-link-button" variant="outline-primary" size="sm" v-b-modal.modal-1 @click="modalEdit(link), changeLink()">editar</b-button>
                                <b-button id="editar-link-button" variant="outline-primary" size="sm" @click="deleteLink(link.id)">deletar</b-button>
                            </div>
                        </div>
                        <b-button variant="outline-primary" id="adicionar-link" v-b-modal.modal-3 v-if="selected.id"> <b-icon icon="plus"></b-icon> Adicionar mais URL</b-button>
                    </div>
                </div>
            </div>
        </b-modal>
        <b-modal id="modal-1" class="z-index-master" title="Editar link" hide-header-close centered hide-footer>
            <div v-if="selectedLink" id="div-edit-link">
                <input type="text" class="edit-link" id="edit-link-name" v-model="selectedChangeLink.link">
                <br><br>
                <input type="number" class="edit-link" id="edit-link-limit" v-model="selectedChangeLink.limit">
                <br><br>
                <div id="buttons-edit">
                    <b-button @click="$bvModal.hide('modal-1')">Cancelar</b-button>
                    <b-button id="salvar-edit-links" variant="primary" @click="saveLink(), update()">Salvar</b-button>
                </div>
            </div>
        </b-modal>
        <b-modal id="modal-2" title="Editar" hide-header-close centered hide-footer>
            <div v-if="selectedLink" id="div-edit-link">
                <input type="text" class="edit-link" id="edit-link-name" v-model="selectedChange.name">
                <br><br>
                <input type="text" class="edit-link" id="edit-link-limit" v-model="selectedChange.hash">
                <br><br>
                <div id="buttons-edit">
                    <b-button @click="$bvModal.hide('modal-2'), update()">Cancelar</b-button>
                    <b-button id="salvar-edit-links" variant="primary" @click="saveRedirect()">Salvar</b-button>
                </div>
            </div>
        </b-modal>
        <b-modal id="modal-3" title="Criar link" hide-header-close centered hide-footer>
            <div v-if="selectedLink" id="div-edit-link">
                <input type="text" class="edit-link" id="edit-link-name" v-model="add_link" placeholder="link">
                <br><br>
                <input type="number" class="edit-link" id="edit-link-limit" v-model="add_limit" placeholder="limite">
                <br><br>
                <div id="buttons-edit">
                    <b-button @click="$bvModal.hide('modal-3'), update()">Cancelar</b-button>
                    <b-button id="salvar-edit-links" variant="primary" @click="addLinks(), $bvModal.hide('modal-3')">Salvar</b-button>
                </div>
            </div>
        </b-modal>
        <b-sidebar id="sidebar-1" shadow right no-header backdrop>
            <div id="header-side">
                <h4>Criação de Link</h4>
                <svg v-b-toggle.sidebar-1 width="18" height="18" viewBox="0 0 18 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M16.7844 1.30859L1.78442 16.3086M1.78442 1.30859L16.7844 16.3086" stroke="#B5B9C5" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
            </div>
            <div id="body-side">
                <div id="name-div-side">
                    <p id="h5-side">Nome do link</p>
                    <input type="text" class="input-side" placeholder="Insira o nome do link" v-model="create_name">
                </div>
                <div>
                    <p class="title-info-side">URL original</p>
                    <p class="info-side">Você poderá inserir uma ou várias URL’s, faça como desejar. Lembre-se de inserir a quantidade de cliques junto à URL.</p>
                </div>
                <div id="false-table"  v-for="object, index in list" :key="index" :list="list"> 
                    <p id="id-side">{{ index +1 }}</p>
                    <p class="inputsLinks" type="text" id="url-side" placeholder="Link">{{ object.link }}</p>
                    <p class="inputsLinks" id="limit-side" type="number" placeholder="Limite">{{ object.limit }}</p>
                </div>
                <div id="div-inputs-links">
                    <input class="inputsLinks" type="text" id="url-side" v-model="create_link" placeholder="Link">
                    <input class="inputsLinks" id="limit-side" type="number" v-model="create_limit" placeholder="Limite">
                </div>
                <b-button variant="outline-primary" id="adicionar-side" @click="saveLinks()"> <b-icon icon="plus"></b-icon> Adicionar mais URL</b-button>
                <div>
                    <p class="title-info-side">URL Default</p>
                    <p class="info-side">Essa URL será associada ao redirecionamento apenas quando todas as outras chegarem ao limite de cliques. Ela será a uma url fixa sem limitação.</p>
                    <input class="input-side" placeholder="Insira a URL Default" v-model="create_hash">
                </div>
            </div>
            <div id="div-salvar">
                <b-button id="salvar-side" variant="primary" size="lg" @click="$bvModal.show('modal'), create()" v-b-toggle.sidebar-1> Salvar Link &#128170</b-button>
            </div>
        </b-sidebar>
    </div>
</template>

<script>
    import axios from "axios";
    import moment from "moment";
    moment.locale("pt-br");
    
    
    export default {
        name: 'modal',
        data() {
            return {
                redirects: [],
                links: [],
                selected: [],
                selectedChange:[],
                selectedLink: [],
                selectedChangeLink: [],
                newTitle: "",
                link: "",
                limit:"",
                lines: 1,
                create_name: "",
                create_hash: "",
                create_total_limit: "",
                create_link:"",
                create_limit:"",
                selectedOne: [],
                list: [],
                add_link: "",
                add_limit: "",
            };
        },

        methods: {
            getDate: function (date) {
            return moment(date, "YYYY-MM-DD").format(
                "DD/MM/YYYY"
            );
            },
            getRedirect() {
            var that = this;
            axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {
                that.redirects = resp.data.redirects;
            });
            },

            getLinks() {
            var that = this;
            axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {
                that.links = resp.data;
            });
            },

            deleteLink(id) {
            var that = this;
            axios.delete("http://127.0.0.1:8000/api/links/" + id).then(function (){
            axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {that.redirects = resp.data.redirects;});  
            axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {that.links = resp.data;});
            });

            },

            select(info) {
                this.selected = info;
            },

            copy(info) {
                navigator.clipboard.writeText(info)
            },

            modalEdit(info) {
                this.selectedLink = info;
            },

            changeLink () {
                this.selectedChangeLink = JSON.parse(JSON.stringify(this.selectedLink));
            },

            saveLink () {
                var that = this;
                var data = {
                id_redirect: this.selectedChangeLink.id_redirect,
                limit: this.selectedChangeLink.limit,
                click: this.selectedChangeLink.click,
                link: this.selectedChangeLink.link,
                };

                axios.put("http://127.0.0.1:8000/api/links/" + this.selectedLink.id, data).then(function (){
                axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {that.redirects = resp.data.redirects;});  
                axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {that.links = resp.data;});
                that.selectedLink=that.selectedChangeLink;
                });
                this.$bvModal.hide('modal-1');
            },

            changeRedirect () {
                this.selectedChange = JSON.parse(JSON.stringify(this.selected));
            },

            saveRedirect () {
                var that = this;
                var data = {
                name: this.selectedChange.name,
                hash: this.selectedChange.hash,
                total_limit: this.selectedChange.total_limit,
                };

                axios.put("http://127.0.0.1:8000/api/redirect/" + this.selected.id, data).then(function (){
                axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {that.redirects = resp.data.redirects;});  
                axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {that.links = resp.data;});
                that.selected=that.selectedChange;
                });
                this.$bvModal.hide('modal-2');
                this.create_link = "";
                this.create_limit = "";
            },

            saveLinks () {
                var data2 = {
                    id_redirect: this.selectedOne.id,
                    link: this.create_link,
                    limit: this.create_limit,
                    click: 0
                };

                axios.post("http://127.0.0.1:8000/api/links", data2).then((resp) => console.log("teste"), this.lines += 1).catch(error => this.lines -= 1);
                this.list.push(data2)
                this.create_link = "";
                this.create_limit = "";
            },

            createNew() {
                var that = this
                var data = {
                    id: "",
                    name: "novo link",
                    hash: "nova url default",
                    total_limit: "0"
                };
                
                axios.post("http://127.0.0.1:8000/api/redirect", data).then(this.selectedOne = this.redirects[this.redirects.length - 1]);
                this.counter = 1;
                this.list = [];
            },

            create() {
                var that = this
                var data = {
                    name: this.create_name,
                    hash: this.create_hash,
                    total_limit: 0
                };

                axios.put("http://127.0.0.1:8000/api/redirect/" + this.selectedOne.id, data).then(function (){
                axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {that.redirects = resp.data.redirects;});  
                axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {that.links = resp.data;});
                });
                this.create_name = "";
                this.create_hash = "";
            },

            createLink (){
                var data2 = {
                    id_redirect: "",
                    link: this.create_link,
                    limit: this.create_limit,
                    click: this.create_click
                }

                axios.post("http://127.0.0.1:8000/api/links", data2).then(console.log('sucesso'));
            },

            update() {
                var that = this;

                axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {
                    that.redirects = resp.data.redirects;
                });

                axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {
                    that.links = resp.data;
                });
            },

            deleteRedirect() {
                var that = this;
                axios.delete("http://127.0.0.1:8000/api/redirect/" + this.selected.id).then(function (){
                axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {that.redirects = resp.data.redirects;});  
                axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {that.links = resp.data;});
                });
                this.selected = []
            },

            addLinks() {
                var that = this;
                var data = {
                    id_redirect: this.selected.id,
                    link: this.add_link,
                    limit: this.add_limit,
                    click: 0
                };

                axios.post("http://127.0.0.1:8000/api/links", data).then(function (){
                axios.get("http://127.0.0.1:8000/api/redirect").then(function (resp) {that.redirects = resp.data.redirects;});  
                axios.get("http://127.0.0.1:8000/api/links").then(function (resp) {that.links = resp.data;});
                });
            },
        },

        mounted() {
        this.getRedirect();
        this.getLinks();
        },
    };
</script>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;500&family=Lato:ital,wght@0,300;0,400;0,700;0,900;1,100;1,300;1,400;1,700;1,900&family=Montserrat:wght@500;600&display=swap");
    * {
    font-family: "Montserrat", sans-serif!important;
    }
    div.modal.my-modal .modal-dialog {
    width: 90% !important;
    }

    #adicionar-link {
        margin-top: 20px;
    }

    #only-for-margin {
        margin-right: 10px;
        display: flex;
        float: right;
        margin-left: -10%;
    }

    #links-links {
        overflow-y: auto;
        overflow-x: hidden;
        height: 90%;
    }

    #modal {
        width: 100%;
    }

    #subtitle {
        font-weight: 400;
        font-size: small;
        align-items: center;
        color: #81858E;
    }

    #main {
        height: 68vh;
        width: 100%;
        display: flex;
        flex-direction: row;
    }

    #title {
        width: 59vw;
        justify-content: space-between;
    }

    #div-title {
        padding: 1vh;
        margin-top: 2vh;
        margin-left: 1.5vw;
        display: inline-block;
        
    }

    #redirect {
        height: 100%;
        width: 40%;
        padding: 3vh;
    }

    #criar {
        padding: 1vh;
        border: none;
        padding-left: 5vh;
        padding-right: 5vh;
        border-radius: 8px;
        background-color: rgba(33, 51, 210, 0.1);
        color: #2133D2;
        margin-right: 2vw;
    }

    #buttons {
        width: fit-content;
        float: right;
        justify-content: right;
        padding: 1vh;
    }

    #X {
        float: right;
        border: none;
        background-color: transparent;
        margin-top: -0.8vh;
        margin-bottom: 1vh
    }

    #red-info {
        display: flex;
        margin-top: -2vh;
        border-bottom: 1px solid #E5E5E5;
    }

    #number-links {
        color: #2133D2;
        font-weight: 600;
        font-family: 'Montserrat';
    }

    #click-p {
        text-align: right;
        width: 85%;
        font-size: small;
        font-weight: 200;
        letter-spacing: 0.4px;
    }

    .redirects {
        height: 97%;
        overflow-y: auto;
    }

    .red-links{
        padding-top: 2vh;
        padding-bottom: 1vh;
        border-bottom: 1px solid #E5E5E5;
    }

    .red-links:hover{
        color: #0b1781;
    }

    .name-date {
        display: flex;
        gap: 15px;
        align-items: center;
    }

    .name {
        font-weight: 600;
    }

    .date {
        font-size: small;
    }

    #links {
        height: 100%;
        width: 60%;
        padding: 40px;
        margin-left: 10px;
    }

    .url-click {
        display: flex;
        justify-content: space-between;
        gap: 20px;
        align-items: center;
        font-size: small;
        margin-top: -5px;
    }

    .cliques {
        margin-right: 10px;
    }

    #name-date-links {
        display: flex;
        gap: 20px;
        align-items: center;
    }

    #name-links {
        font-size: x-large;
        font-weight: 600;
    }

    #date-links {
        font-size: small;
    }

    #url-links {
        color: #2133D2;
    }

    #url-buttons-links {
        display: flex;
        gap: 20px;
        height: 30px;

    }

    #copiar-links{
        width: 90px;
    }

    #editar-links{
        width: 90px;
    }

    #false-table-links {
        display: flex;
        justify-content: space-between;     
        height: 60px;
        margin-top: 20px;
        align-items: top;
    }

    #id-links {
        font-weight: 800;
        color: #2133D2;
        margin-left: 10px;
        margin-right: 10px;
    }

    #url-limit {
        height: 40px;
    }

    #url-link {
        font-size: small;
        width: 420px;
        border: none;
        background-color: transparent;
        color: #81858E;
    }

    #limit-links {
        font-size: small;
        color: #2133D2;
    }

    #editar-link-button {
        height: 30px;
        width: 90px;
        margin-right: 8px;
    }

    #sidebar-1 {
        width: 45vw;
    }

    #header-side {
        width: 100%;
        background-color: #191B28;
        color: #E5E5E5;
        position: relative;
        padding: 20px;
        justify-content: space-between;
        display: flex;
        align-items: center;
        padding-left: 40px;
        padding-right: 40px;
    }

    #name-div-side {
        margin-bottom: 2vh;
    }

    #body-side {
        margin: 55px;
        height: 73%;
        max-height: 73%;
        overflow-y: auto;
    }

    #h5-side {
        color: #191B28;
        font-weight: 600;
        font-size: large;
    }

    .input-side {
        border: none;
        border-bottom: 1px solid #979797;
        padding: 10px;
        width: 100%;
        background-color: transparent;
    }

    .input-side:focus, .input-side:hover {
        border-bottom: 1px solid #050c44;
    }

    .info-side {
        color: #81858E;
    }

    .title-info-side {
        color: #2133D2;
        font-size: x-large;
        font-weight: 600;
        margin-bottom: 0;
    }

    #false-table {
        display: flex;
        gap: 10px;
        justify-content: space-between;
        width: 90%;
        height: 70px;
        align-items: center;
        margin-left: 10px;
    }

    #id-side {
        width: 10%;
        font-weight: 800;
    }

    #url-side {
        width: 80%;
        color: #81858E;
    }

    #limit-side {
        width: 10%;
        color: #81858E;
    }

    #adicionar-side {
        margin-bottom: 30px;
        border-color: #2133D2;
        color: #2133D2;
    }

    #adicionar-side:hover {
        border-color: #2133D2;
        color: #ffffff;
        background-color: #2133D2;
    }

    #div-salvar {
        justify-items: right;
        margin-right: 60px;
    }

    #salvar-side {
        float: right;
        width: 250px;
        background-color: #2133D2;
        border: #2133D2;
    }   

    #salvar-side:hover {
        background-color: #0f1c97;
        border: #0f1c97;
    }

    #div-edit-link {
        padding: 15px;
    }

    .edit-link {
        border: none;
        padding: 9px;
        border-radius: 10px;
        background-color: #81858e27;
        width: 100%;
    }

    #salvar-edit-links {
        float: right;
    }

    .inputsLinks {
        border: none;
        border-bottom: 1px solid gray;
        background-color: transparent;
        padding: 5px;
    }
    
    .inputsLinks:hover, .inputsLinks:focus {
        border-bottom: 1px solid #0f1c97;
    }

    #div-inputs-links {
        margin-left: 7%;
        gap: 10px;
        display: flex;
        margin-bottom: 20px;
    }
</style>