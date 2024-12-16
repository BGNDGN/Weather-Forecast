<template>
    <div>
        <input type="text" placeholder="Lütfen bir şehir ismi giriniz." v-model="sehir">
        <button @click="value">Gir</button>
    </div>
    <div>
        <p>{{ yazi.sicaklik }}</p>
        <p>{{ yazi.aciklama }}</p>
        <p>{{ yazi.ülke }}</p>
        <p>{{ yazi.isim }}</p>
        <img :src = "require(`@/assets/${hava.image}`)" :alt="hava.aciklama"/>
    </div>
</template>

<script>

import axios from "axios"

export default {
    data() {
        yazi=""
        sehir=""
        hava=[]
    },

    async asyncData() {
        try {
            const anahtar = "1725f4c3e35e4ee08a1151139240712";
            const istekat = await axios.get(`https://api.weatherapi.com/v1/current.json?q=${this.sehir}&key=${anahtar}`);

            this.yazi= {
                sicaklik: istekat.data.current.temp_c,
                aciklama: istekat.data.current.condition.text,
                ülke: istekat.data.location.country,
                isim: istekat.data.location.name,
            };

            if(this.yazi.aciklama==="Partly Cloudly") {
                this.hava.push({id:1, image:'resim1.jpg', name:'parçalı bulutlu'})
            }

        } catch (error) {
            
        }
    },

    methods: {
        async value() {
            if(this.sehir==="") {
                alert("Lütfen bir şehir ismi girin.")
            }

            this.hava=[];
            await this.asyncData();
            this.sehir="";
        }
    }
}
</script>