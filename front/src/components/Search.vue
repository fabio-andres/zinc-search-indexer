<script setup lang="ts">
import { ref, Ref, onMounted } from "vue"
import RootObject from "../services/searchService/interface";

//text esta enlazada al atributo value del elemento html <input> usado dentro de <template>, osea text contiene el texto ingresado en un elemento <input>
const text: Ref<string> = ref("")
const emit = defineEmits(['reqData'])

const req = async () => {
    const user: string = "admin"
    const password: string = import.meta.env.VITE_ZINC_SEARCH_PASSWORD
    const encodeCredentials: string = btoa(user + ":" + password)
    const url: string = import.meta.env.VITE_URL
    
    const reqOptions = {
        method: "POST",
        headers: {
            "Authorization": "Basic " + encodeCredentials,
            "Content-Type": "application/json",
        },
        body: JSON.stringify({
            "query": {
                "match_phrase": {
                    "document.Body": text.value
                }
            }
        }),
    };

    try {
        const res = await fetch(url, reqOptions)
        const data: Promise<RootObject> = await res.json();
        if (res.status === 200) {
            console.log("request successful", res.status, res.statusText)
            emit("reqData", data)
        } else {
            console.log(res.status, res.statusText)
        }
        console.log(data)
    } catch (error) {
        console.log('error al consumir api', error);
    }
}

</script>

<template>
    <section class="main-search">
        <button @click="req"><span class="material-icons-round main-search__search-icon">search</span></button>
        <input class="main-search__input" type="text" placeholder="search email message" v-model="text">
    </section>
</template>

<style scoped>
span {
    color: #6366f1;
    font-size: 27px;
}

span:hover {
    box-shadow: 0 2px 12px gray;
    cursor: pointer;
}

.main-search {
    background-color: #111827;
    width: 580px;
    border-radius: 101px;
    border: 1px solid #6366f1;
    margin: 0 auto;
    margin-bottom: 35px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.main-search:hover {
    box-shadow: 0 4px 24px gray;
}

.main-search__input {
    background-color: #111827;
    width: 500px;
    height: 40px;
    border: none;
    outline: none;
    color: #ffffffee;
    font-size: 19px;
}

.main-search__micro-icon {
    background-position: center;
    background-size: contain;
    width: 16px;
    height: 21px;
    cursor: pointer;
    margin: 0;
}

button {
    background-color: #111827;
    border: none;
}
</style>