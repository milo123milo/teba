# Teba - ecommerce web

Frontend framework: [Vue3](https://vuejs.org/guide/quick-start.html)

CMS (Content Managment System): [Strapi v4](https://docs.strapi.io/developer-docs/latest/getting-started/quick-start.html)

State Managment: [Pinia](https://pinia.vuejs.org/)

GraphQL Client: [Villus](https://villus.logaretm.com/)

Language Router: [Vue Language Router](https://github.com/adbrosaci/vue-lang-router/tree/vue-3)

ApiEngine: [GraphQL](https://docs.strapi.io/developer-docs/latest/plugins/graphql.html)

Markdown Parser: [markdown-it](https://www.npmjs.com/package/vue3-markdown-it)

Site map: [Link](https://octopus.do/mupjg7vtnwk)

Design guide: [TebaDesign-Figma](https://www.figma.com/file/ESlx3igS8SYZfqehCNUWKK/TEBA)

---

## Collection Types za Strapi

Tipovi podataka koji se u Strapiju mogu unositi kao liste, tj strapi moze da cuva vise njih u jednom Struktura je na mapi. *Pogledati dokumentaciju za Collection Types.*



- Product

- Product List

- News
  
  

Objekti koji će se povlačiti sa API-ja. Osim Product Liste koja je zapravo sadržina korpe.

Na mapi u sekciji *TYPES*, zelenom bojom su funkcije koje treba da posjeduje koje se dodaju kao logika u javascript kodu u frontendu.

---

### Home

Struktura se sastoji od sekcija koje su date na mapi. Skecije po mogućnosti popunjavati materijalima sa Strapija. Ono što mora bit hardkodovano radi ubrazanja razvoja neka bude. 

U kodu svaku sekciju odvojiti kao posebni modul koji se kasnije poziva u jednom zajedničkom View-u ("npr. HomeView")



**HomeView**

- NavBar

- Landing

- Products

- LatestNews

- Footer



##### Landing



##### Products



##### Latest News



---

### NavBar

Univerzalni modul koji će se pozivati na početku svih prozora osim u prozoru **Cart** i **Checkout**.

Responzijom prelazi u [Burger-Menu.](https://www.npmjs.com/package/vue3-burger-menu) Vue3 Modul za izradu burger menija.

---

### Store

Stranica koja sadrzi sve proizvode. Mogućnost predstavljanja proizvoda po kategorijama kao i mogućnost prikazivanja svih proizvoda. Ovo riješiti pozivanjem novog Api zahtjeva za odgovarajućim parametrima.



**Products filter**

Filtiranje se radi ponovnim pozivanjem API-ja sa prosleđenim željenim filterom koje CMS nudi. *Pogledati dokumentaciju ApiEngina za dostupne filtere sortiranja.*

---

### Cart

Treba da sadrzi listu izabranih proizvoda i da racuna njihovu ukupnu cijenu.

Dodavanje, uklanjanje i cuvanje proizvoda se vrši bibliotekom **Pinia**. *Pogledati prilozenu dokumentaciju na vrhu.* 



### Checkout

Preuzima informacije za isporuku proizvoda od korisnika. Na osnovu izabrane zemlje za posiljku, porudžbenica se salje na mail predstavništva za tu zemlju.

---

### News

Stranica koja sadrzi sve vijesti.



**News filter**

Filtiranje se radi ponovnim pozivanjem API-ja sa prosleđenim željenim filterom koje CMS nudi. *Pogledati dokumentaciju ApiEngina za dostupne filtere sortiranja.

---

### About Us, Contac Us

Sav sadržaj povlačiti sa CMS-a. 

---

### Smjernice za razvoj

U prilogu se mogu naći pomagala, dijelovi koda i riješenja za probleme koji se mogu pojaviti u toku razvoja.





**GraphQL Client Villus - Primjer pozivanja**

```js
<script>

// Importovanje biblioteke
import { useQuery } from 'villus';

export default {
   // Vue3 setup(){}
   setup() {
    
    // Konstanta 'kontakt' koja cuva izgled poziva
    const kontakt = `
    query {
        kontakt {
            data {
                attributes {
                        info
                }
            }
        }
    }
    `;

    // Objekat data koji cuva rezultat pozivanja.
    // Funckija useQuery uzima izgled poziva 'kontakt' i vrace 
    // odgovor sa servera.
    const { data } = useQuery({
      query: kontakt,
    });

    // Napunjen objekat, koji se sada samo u obliku 'data' 
    // Moze koristit kroz Vue HTML elemente
    return { data };

    

  },
  
}

</script>
```

**Implementacija Api objekta 'data' u HTML elementima**

```html
<template>
 <div v-if="data" >{{ data.kontakt.data.atributes.info }} </div>
</template>
```

Obavezno je provjeriti da li je *data* null ili nije. To radimo sa *v-if="data"*. Kasnije u zavisnosti od zahtijeva parsujemo ga gdje zelimo kao obican objekat.



**Preporuka za pozivanje apija**

Ako odredjeni View prozor sadrzi vise komponenti koji trebaju informacije sa CMS, a primjerice se te informacije mogu povući sa istog zahtijeva, preporuka je zahtijev slati u krajnjem sadržaocu svih elemenata, pa onda dobijeni objekat proslijediti u komponente uz pomoć *v-bind-a* i *props-a*. Sto je više moguće praktikovati ovaj princip zbog kasnije sveobuhvatne optimizacije aplikacije.

<img src="file:///C:/Users/milop/Documents/sau/dijagram.drawio.png" title="" alt="dijagram.drawio.png" data-align="center">

**API Pagination**

Zbog re-rendera. 

```js
export default {
     components: {
        NewsMoreContainer,
  },
  data(){
    return {
      showup: true,
      showdown: false
    }
  },
  computed: {
    pagein: function() {
      if(this.count >= 6){
        this.showdown = true;
      }else{
        this.showdown = false;
      }
      if(this.data.vijestis.data == 0){
        this.count = 0;
        return this.data.vijestis.data
      } else {
        return this.data.vijestis.data
      };
    }
  }
  ,
  methods: {
    pageup: function() {
      this.count += 6;
    },
    pagedown: function() {
      if(this.count >= 6){
        
        this.count -= 6;
      }
    }
  },
  setup() {
    const { t } = useI18n({});  
    
    const count = ref(0);
    const naslov = "Vijesti" + t('apiN');
    const sadrzaj = "Vijesti" + t('apiS');
    const vijestis = computed(() => { 
      return `
    query vijestis {
 vijestis(pagination: {start: ${count.value}, limit: 6}, sort: "id:desc")
 {
  data {
    id
    attributes {
       createdAt
       VijestiProfilna {
        data {
          attributes {
            url
          }
        }
      }
      ${naslov}
      ${sadrzaj}
      
    }
  }
}
}
    `; });

    
    const { data } = useQuery({
      query: vijestis,
    });

   
    


    return { 
      data,
      count,
      
       };

  }
}
```

```html
<template>

    <div class="newsmoreview">
      
         <Menu class="colorblue"></Menu>
         
        <div id="conter container-fluid">

      <news-more-container 
        v-if="data" v-bind:btapiitem="pagein" 
        class="timecontainer">
        </news-more-container>
     
   
    <div class="arrowscont">
      <img id="arrowright" 
        class="arrowpage" 
        v-show="showup" v-if="data" 
         @click="pageup(data.vijestis.data)" 
            src="@/assets/arrow.svg">
      
        
        <img id="arrowleft" 
            class="arrowpage" 
            v-show="showdown" 
               v-if="data" 
                @click="pagedown(data.vijestis.data)" 
            src="@/assets/arrow.svg">

      </div>
        </div>
    </div>

</template>
```

**Vue Langauge Router - Instalacija i primjeri**

Većina bi trebalo sama da se podesi. U slucaju da ovom komandom ne uspije instalacije, pogledati dokumentaciju bilbioteke. Fajlovi main.js i index.js bi trebalo vec imat ove projmene ali treba provjeriti. U slucaju da je instalacija uspjesna, a ovih promjena nema, dodati ih manualno.



```bash
vue add lang-router
```

```js
// src/main.js
// ... //
import { i18n } from "vue-lang-router";
// ... //
createApp(App).use(i18n).mount('#app')
```

```js
// src/router/index.js
// ... //
import translations from '../lang/translations'
import localizedURLs from '../lang/localized-urls'
import { createLangRouter } from 'vue-lang-router'
// ... //
const langRouterOptions = {
	defaultLanguage: 'me', //Mora imat isti naziv kao fajl prevoda
	translations,
	localizedURLs,
}
// ... //
const router = createLangRouter(langRouterOptions, routerOptions)
```

Ova biblioteka prevode čuva u dva objekta koji će se sami generisati u folderu *src/lang/transltions*. Nije preporučljivo te objekte koristi za hard-kodovano prevođenje već kao određenu logiku za Api zahtjevi ili drugo.

```js
// en.js
{
	"home": "Home",
	"news": "News"
    "info": "info_en"
}


// me.js
{
    "home": "Pocetna",
    "news": "Vijesti"
    "info": "info"
}
```

Ako je neki sučajem ipak potrebno koristiti informacije iz ovih objekata direktno u html to se može uraditi na sledeći način: 

*Nije potrebno importovat biblioteku*

```html
<template>
    <div>{{ $t("home") }}</div>
</template>
```

U zavisnosti da li se nalazimo na URL koji ima "/en" ili "/me" ispis će biti ili "Home" ili "Pocetna". To jest bice definisan sa ta dva objekta.



Ukoliko želimo da pozivamo Api koji šalje različite zahtjeve u odnosu na jezik jedna od mogućnosti će biti data u kodu. 

Primjerice, u CMS su definisana polja "info" i "info_en". Prvo polje sadrzi informacije na crnogorskom drugo na engleskom.

```js
<script>

// Importovanje biblioteke za Api
import { useQuery } from 'villus';
// Dodatna biblioteka za jezike
import { useI18n } from 'vue-i18n'

export default {
  
   setup() {
    // posto nacin pozivanja "$t" radio samo u HTML elementima
    // moramo ga definisati ovdije u kodu radi kasnije koriscenja
    const { t } = useI18n({});

    const info = t('info'); // U zavisnosti od jezika ova konstanta 
                            // ce imati vrijednost info ili info_en
    
    const kontakt = `
    query {
        kontakt {
            data {
                attributes {
                       ${info} // dinamicko upisivanje vrjednosti
                }
            }
        }
    }
    `;

    // Objekat data koji cuva rezultat pozivanja.
    // Funckija useQuery uzima izgled poziva 'kontakt' i vrace 
    // odgovor sa servera.
    const { data } = useQuery({
      query: kontakt,
    });

    // Napunjen objekat, koji se sada samo u obliku 'data' 
    // Moze koristit kroz Vue HTML elemente
    return { data };

    

  },
  
}

</script>
```



**MarkDown Parser - Instalacija i primjeri**

Strapi CMS ima mogućnost čuvanja MD teksta kao i njego prosleđivanje kroz API. Na mapi sajta polja koja imaju prefiks MD će koristiti markdown tip cuvanja teksta. Stoga da bih to prikazali na sjatu moramo imati odredjen parser koji će MD format renderovati u html. Za to koristimo navedenu bilbioteku.

```bash
npm install vue3-markdown-it
```

```js
import Markdown from 'vue3-markdown-it';

export default {
    components: {
    Markdown
  }
}
```

```html
<template>
<div>
 <div id="markdownDiv">
    <Markdown class="" :source="data.MDContent" /> 
 </div>
<div/>
</template>
```

Markdown element mozete tretirati kao svaki drugi HTML element što se tiče CSS-a. Polje ":source" se puni API odgovorom koji sadrži markdown tekst. Markdown element uvijek uključivati u zaseban "div". Za više pročitati dokuemntaciju biblioteke.


