# Productbiografie - De Meldpaal
_Productbiografie van [De Meldpaal](https://github.com/roobinh/meesterproef-1819)_

## Inhoudsopgave
1. [Inleiding](#1)
2. [Het Proces](#2)
   - 2.1 - Week 1
   - 2.2 - Week 2
   - 2.3 - Week 3
   - 2.4 - Week 4
   - 2.5 - Week 5
3. [Leerdoelen](#3)
4. [Reflectie (+ rubric)](#4)

## 1. Inleiding
In dit verslag behandel ik de ontwikkeling van niks tot eindproduct van [De Meldpaal](https://github.com/roobinh/meesterproef-laadpaal). Ik heb het proces opgedeeld in 5 stukken, namelijk de 5 weken dat we ([Jim van der Ven](https://github.com/JimvandeVen) & ik) met de applicatie bezig zijn geweest. Hierin heb ik 60% van de back-end gedaan, en Jim 60% van de front-end. Dit omdat ik meer verstand van back-end heb en Jim meer van front-end, maar we ook wat willen leren van het project. 

## 2. Het Proces

## 2.1 - Week 1
_27/05 - 02/06_

De meesterproef begon (27 mei) met gesprek een met onze opdrachtgever [J.R. Helmus](http://www.hva.nl/profiel/h/e/j.r.helmus/j.r.helmus.html). Hierin werden we aan het onderwerp ingeleid; wat er van ons verwacht werd en wie de stakeholders zijn. Voor het gesprek wist ik niet wat ik moest verwachten, maar na het gesprek was het al stukken duidelijker: een applicatie om ongemak bij laadpalen in Amsterdam te meten.

Na dit gesprek zijn Jim en ik tot dinsdag bezig geweest met concepten en mock-ups bedenken, en we waren er allebei over eens dat de app zo gemakkelijk en snel mogelijk moest werken. Na wat schetsen hebben we een [mock-up](https://xd.adobe.com/view/8d5aadbe-37d5-4410-462f-29fa49052da3-fb6b/) gemaakt in Adobe XD. Naast een mock-up, hebben Jim en ik ook goed nagedacht over de database. Welke mogelijke klachten zijn er, en welke collecties zijn er nodig. 

<details>

<summary>Op dinsdag 28/05 hadden we deze database bedacht</summary>

```
-- USERS -- 
ID: unique ID
email
name
number
points (?)

-- POLES --
ID: laadpaal ID
longitude
latitude

-- COMPLAINTS --
ID: complaint ID
user: user ID
pole: pole id
type
description
image
status
date
```
</p>
</details>



Dinsdag was ook de eerste commit, namelijk de 'lege' NodeJS server. Ook was mongo al geimplementeerd; eerst met lokale data, maar woensdag met een online database (genaamd [Mongo Atlas](https://www.mongodb.com/cloud/atlas)).

_(Omdat het dat weekend hemelvaart was, zijn we maandag verder gegaan)_

## 2.2 - Week 2
_03/06 - 09/06_

De tweede week begon met een gesprek met onze opdrachtgever. Hierin hebben we onze mock-ups laten zien en feedback ontvangen. De mock-ups zagen er goed uit, alleen moesten we het kleurenschema nog aanpassen.

Daarna zijn we gewoon verder gegaan met het maken van de website. Jim heeft zich deze week vooral bezig gehouden met de front-end, en ik met de back-end. 

In het begin was het allerbelangrijkste in de back-end de communicatie met de database, dus het kunnen opslaan & ophalen van data. Voor de communicatie tussen NodeJS (de server) en Mongo Atlas (de database) gebruiken we een framework genaamd GraphQL. GraphQL is lastig om in je server op te zetten, maar eenmaal opgezet is het heel makkelijk om data op te vragen. Dat opvragen gaat dan met hele makkelijke query's. Om GraphQL op te zetten, heb je in de Node server een schema, resolver & models nodig.

Schemas: De Schema's zorgen voor de logica achter de query. Zo zorgt een schema er voor dat de opgegeven query altijd geldig is. Een schema helpt jou een query/mutation te schrijven.

Resolver: Een resolver is een functie die de opgevraagde data terugstuurd naar de server. Een van de functies in de resolver wordt aangeroepen tijdens een request. Dus in de NodeJS server maak je een Query, die query gaat door de resolver en wordt teruggestuurd.

Model: Ons project telde op dit moment 3 models, namelijk de 3 tabellen (collections genoemd) die we in de database hebben. In de model staat alle data die opgeslagen mag worden, en van welk type. Bijvoorbeeld (email: String).

Op 04/06 was het mogelijk om alle palen, users en complaints op te vragen & toe te voegen in database. Het enige wat nu nog ontbrak was het opvragen van een specifieke complaint (aan de hand van het unieke complaintid). Op 05/06 was dit geimplementeerd. Het was nu ook mogelijk om een specieke user/complaint/pole op te vragen uit de database. Voor nu hoefde er niks aangepast te worden aan de database, maar was de volgende stap het inloggen.

Als je op een site moet inloggen met een wachtwoord voordat je een klacht in kan dienen, zorgt dit er voor dat mensen er geen zin in hebben. Daarom hebben we besloten dat je alleen in hoeft te loggen met je email. Secure is het niet maar voor de prove of concept is het prima, omdat we complaints aan specifieke gebruikers koppelen. Inloggen gaat op de volgende manier:
- Op de inlogpagina (eerst pagina) wordt gevraagd om je e-mail adres. Als deze in de database bestaat: maak een session aan, met daarin de userId (uit de database) van deze gebruiker.
- Bestaat de e-mail nog niet in de database, wordt gevraagd je naam op te geven. Deze worden dan samen als nieuwe user in de database gezet, vervolgens log je in.

Einde week 2:
- Database opgezet
- Inlogmogelijkheid
- Styling inlogpagina
- Styling melding maken


## 2.3 - Week 3
_10/06 - 16/06_

In week 3 zijn we verder gegaan met de site. Ik heb me deze week vooral bezig gehouden met het implementeren van MapBox en het weergeven van palen op de kaart (eerst met mock-data). Ons doel was om dit woensdag af te hebben, zodat we dit donderdag aan de opdrachtgever konden laten zien.

Op 12/06 was de kaart geimplementeerd met de volgende functionaliteit:
- De locatie van de gebruiker op de kaart
- 3 'nep' palen
- Bij het drukken op een paal: een pop-up met "melding maken".

Bij het drukken op melding maken, wordt je doorgestuurd naar de 'melding maken' pagina, gemaakt door Jim. Hier kan je je melding selecteren (dus bijvoorbeeld 'De paal is niet te bereiken'). De stap hierna is het opslaan van deze complaint in de database.

Omdat we gebruik maken van GraphQL was het heel makkelijk om data toe te voegen aan de database. De query voor het aanmaken van een complaint:

```javascript
const query = `
    mutation {
        createComplaint(complaintInput: {
          type: "${type}", 
          description: "${description}",
          image: "",
          status: "${status}",
          date: "${dateString}",
          userId: "${userId}",
          poleId: "${poleId}"
        }) 
      }
    `
```

Op donderdag 13/06 hebben we een gesprek gehad met onze opdrachtgever. Hierin hebben we de app laten zien en gevraagd of de databasestructuur logisch is en alle mogelijke klachten hebben opgeschreven. Dit was allemaal prima, en hij was vooral benieuwd naar ons eindresultaat.

Om ons product te verbeteren hebben we onze opdrachtgever gevraagd naar data over palen om op de map weer te geven, die hij meteen heeft toegestuurd. Deze data bevatte 10.000 palen met veel informatie, zoals longitude/latitude, een adres, regio & postcode. Omdat er nu veel meer informatie bij is gekomen hebben we onze onze palen tabel moeten aanpassen. 

<details>
<summary>
De palen collectie ziet er nu zo uit.
</summary>

<img src="https://i.ibb.co/Mkdbfj4/image.png" height="200px">
</details>

We kregen alle palen in een csv bestand maar wouden het in de database, zodat we de unieke paal id kunnen koppelen aan een complaint id. Om alle data uit het CSV bestand in de database te stoppen heb ik een functie geschreven in Javasript. Deze functie doet het volgende:
<details>

<summary>Eerst gaat hij alle palen in het CSV bestand bij langs, om met de nodige data een query-string te maken. Deze query stopt hij vervolgens in een Array.</summary>

``` javascript 
const csv = require('csv-parser');

var allQuerrys = []

async function createPoles() {
    var providers = ["NUON", "EVBOX", "EVNET", "ALVEN"]

    fs.createReadStream('dfLocations.csv')  
        .pipe(csv())
        .on('data', (row) => {
            var country = row.Region
            var postal = row.PostalCode;
            
            if(country == "Amsterdam") {
                var longitude = parseFloat(row.Longitude)
                var latitude = parseFloat(row.Latitude)
                var city = row.City
                var region = row.Region
                var regioncode = row.State
                var district = row.District
                var subdistrict = row.SubDistrict
                var address = row.Address
                var postalcode = row.PostalCode
                var randomprovider = providers[Math.floor(Math.random() * providers.length)]
                var provider = randomprovider
                var sockets = Math.floor(Math.random() * 2) + 1
                var usedsockets = Math.ceil(Math.random() * sockets)
                
                if(longitude && latitude) {

                    const query = `
                    mutation {
                        createPole(poleInput: {
                            longitude: ${longitude},
                            latitude: ${latitude},
                            city: "${city}",
                            region: "${region}",
                            regioncode: "${regioncode}",
                            district: "${district}",
                            subdistrict: "${subdistrict}",
                            address: "${address}",
                            postalcode: "${postalcode}",
                            provider: "${provider}",
                            sockets: ${sockets},
                            usedsockets: ${usedsockets}
                            }
                        ) {
                          city
                          region
                          regioncode
                          district
                          subdistrict
                          address
                          postalcode
                          provider
                          sockets
                          usedsockets
                        }
                    }
                    `

                    allQuerrys.push(query)
                           }       
        }})
        .on('end', () => {
            console.log('all querrys added..');
            console.log(allQuerrys.length)
            addToDB(0)
        }
    );    
}
```
</details>


<details>

<summary>Nu ik een Array heb met alle (ongeveer 1500) query's, was het tijd om deze naar de database te sturen. Omdat de database niet 1500 requests in 1 keer kan handelen (heb ik geprobeerd, maar hier crashte hij uit), stuur ik pas een nieuwe request als de vorige is gestuurd.</summary>

```javascript
function addToDB(number) {
    console.log('adding to db...' + number);

    var query = allQuerrys[number]
    return fetch('http://localhost:2500/graphql', {
        method: "POST",
        headers: { "content-type": "application/json" },
        body: JSON.stringify({ query }),
    })  
        .then(response => response.json())
        .then(data => {
            if(number <= allQuerrys.length) {
                addToDB(number+1);

                if (data.errors) {
                    console.log("Complaint unsuccessfully created")
                } else {
                    console.log("--- Complaint aangemaakt ---")
                    // complaint succesfull, send next complaint
                    addToDB(number+1); 
                }
            }
        });
}
```

</details>

De dag 
Einde week 3:
- Inloggen
- MapBox met echte palen
- Mogelijk om een melding van een paal te maken
- Mogelijkheid om 'jouw' meldingen te bekijken.


## 2.4 - Week 4
_17/06 - 23/06_

Omdat de core functionaliteit van de app al behaald is, was het tijd voor de laatste loodjes aan de app. Omdat de opdrachtgever op vakantie was, hebben we deze week geen gesprek gehad. Wel hebben Jim en ik aan het begin van de week bedacht wat de belangrijkste verbeterpunten waren. Er waren namelijk nog wat punten die verbeterd/toegevoegd moesten worden:


<details>
<summary>
Omdat de kaart met 1500 palen erg haperde, heb ik eerst 'clusters' toegevoegd aan de kaart. Dit voegt de palen die op elkaar staan toe aan 1 bolletje, zodat er 300 palen in 1 bolletje worden weergegeven. Dit zorgt voor een enorme performance boost.
</summary>

```javascript
map.on("click", "unclustered-point", function(e) {
    const address = e.features[0].properties.address;
    const id = e.features[0].properties.id;
    const lnglat = e.features[0].properties.lngLat.split(" ");
    let mapbutton;
    if (e.features[0].properties.showComplaints == 1) {
        mapbutton = "";
    } else {
        mapbutton = "grey";
    }
    let html = `
        <h3>${address}</h3>
        <p><a class="mapbutton" href="/setpole/${id}">Melding maken</a></p>
        <p><a class="mapbutton ${mapbutton}" href="/reports/${id}">Meldingen bekijken</a></p>
        <p><a class="mapbutton" href="https://maps.google.com/?q=${
        lnglat[1]
        },${lnglat[0]}">Navigeren</a></p>
    `;

    new mapboxgl.Popup()
        .setLngLat(e.features[0].properties.lngLat.split(" "))
        .setHTML(html)
        .addTo(map);
    });

    map.addLayer({
    id: "cluster-count",
    type: "symbol",
    source: "earthquakes",
    filter: ["has", "point_count"],
    layout: {
        "text-field": "{point_count_abbreviated}",
        "text-font": ["DIN Offc Pro Medium", "Arial Unicode MS Bold"],
        "text-size": 12
    }
    });

    map.addLayer({
    id: "unclustered-point",
    type: "circle",
    source: "earthquakes",
    filter: ["!", ["has", "point_count"]],
    paint: {
        "circle-color": ["get", "color"],
        "circle-radius": 8,
        "circle-stroke-width": 4,
        "circle-stroke-color": ["get", "color"],
        "circle-stroke-opacity": 0.5
    }
    });
});
```

</details>

<details>
<summary>
Omdat de terugknop op elke pagina wordt weergegeven en nu alleen nog maar een pagina terug gaat, heb ik hier logica per pagin aan toegevoegd.  
</summary>

```javascript
document.querySelector(".backArrow").addEventListener("click", function() {
  back();
});

function back() {
  switch (href) {
    case "reports":
      window.history.back();
      break;
    case "myreports":
      window.history.back();
      break;
    case "create":
      previousOption();
      break;
    default:
      window.history.back();
      break;
  }
  if (href == "reports") {
  }
}
```
</details>

<details>
<summary>
Omdat het soms lastig is om te zoeken naar de dichstbijzijnde paal, hebben we hier een knop voor toegevoegd. Deze knop is grijs bij het laden van de pagina, en wordt groen (en klikbaar) op het moment dat de dichstbijzijnde paal is berekend. 
</summary>

```javascript
function calculateNearestPole(long, lat) {
    availablepoles.forEach(pole => {
      const distance = calculateDistance(lat, long, pole[1], pole[0]);

      if (distance < dichstbijzijnde[0]) {
        dichstbijzijnde = [distance, pole[1], pole[0]];
      }
    });
    if(dichstbijzijnde[0] !== 99999) {
      console.log("nearest pole calculated." + dichstbijzijnde)
      document.querySelector('#fly').setAttribute('style', 'background: #2F855A;')
      document.querySelector('#fly').disabled = false;
    }
  }

  function calculateDistance(lat1, lon1, lat2, lon2) {
    const R = 6371; // Radius of the earth in km
    let dLat = deg2rad(lat2 - lat1); // deg2rad below
    let dLon = deg2rad(lon2 - lon1);
    let a =
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos(deg2rad(lat1)) *
        Math.cos(deg2rad(lat2)) *
        Math.sin(dLon / 2) *
        Math.sin(dLon / 2);
    let c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    let d = R * c; // Distance in km
    return d;
  }

  function deg2rad(deg) {
    return deg * (Math.PI / 180);
  }
```

</details>

<details>
<summary>
Als je op een gele paal drukt, kan je alle complaints van die paal bekijken. Zo is iedereen op de hoogte van meldingen over alle palen.
</summary>
<img src="https://i.ibb.co/qBYDb7H/image.png" height="500px">
</details>

</details>

<details>
<summary>
Het was al wel mogelijk om een melding aan te maken, maar hier was nog geen chat aan gekoppeld. Eerst hebben we een nieuwe tabel in de database toegevoegd genaamd "messages". Alle messages zijn gekoppeld aan een complaintId. Zo kan je makkelijk vanuit de server alle berichten van 1 complaint opvragen. Bij het openen van een complaint (moet wel je eigen zijn), maakt de client een socket verbinding met de server. Deze stuurt vervolgens alle berichten van de complaint naar de client. Als de client nu een nieuw bericht stuurt, gaat deze via de socket verbinding naar de server, in de database, en naar iedereen in de room. Zo zou je makkelijk vanuit het dashboard met de klant kunnen chatten.

</summary>

```javascript
app.get('/account/melding/:id', authenticate, function(req, res, next) {
    // sockets
    let io = socket(server);

    const complaintId = req.params.id;

    // when user connects
    io.on('connection', function(socket) {
        console.log('a user connected');

        socket.on('join-room', function(data) {

            // set room number
            var room = data;

            // joining user to room
            socket.join(room);

            console.log("room joined! id = " + room)


            if(!req.session.room) {
                console.log("new room, sending messages.")
                req.session.room = {
                    id: room
                }
                req.session.save()
                socketSendChat(room, io);
            } else {
                if(req.session.room.id !== room) {
                    console.log('room changed! sending new messages.')
                    req.session.room = {
                        id: room
                    }
                    socketSendChat(room, io);
                    req.session.save()
                } else {
                    console.log('else')
                    socketSendChat(room, io);
                }           
            }
            })

        socket.on('disconnect', function() {
            console.log('user disconnected');
        });

        socket.on('new-client-message', function(message) {
            console.log("message recieved!")
            console.log(message);
            writeMessageToDatabase(message, complaintId, req.session.user.id, io);
        })

    });
```
</details>

## 2.5 - Week 5
_24/06 - 28/06_

De laatste week. In deze week hebben we niet veel meer aangepast aan de app. Alleen wat kleine UX changes (zoals onboarding) en bug fixes. We zijn vooral bezig geweest met alle documentatie. 

## 3. Leerdoelen
_Alle technieken die ik bij de meesterproef wil gaan toepassen_

- [web-app-from-scratch](https://github.com/roobinh/web-app-from-scratch-18-19)
 
 Web app from scratch gaat vooral om het aanbrengen van structuur in je code & data ophalen/manipuleren. Tijdens dit project ga ik werken met twee nieuwe technieken (GraphQL & MongoDB), nettere code proberen te schrijven en gebruik maken van IIFE's waar nodig.

- [css-to-the-resque](https://github.com/roobinh/css-to-the-rescue-1819)
  
Omdat ik niet goed ben in het stylen van sites, vind ik het belangrijk zelf ook styling te doen op onze site. Hier ga ik dan ook, naast de back-end, zo veel mogelijk mee bezig.

- [web-design](https://github.com/roobinh/css-to-the-rescue-1819)
  
We gaan proberen zo veel mogelijk rekening te houden met kleurenschemas en veel tests laten doen door andere mensen. Omdat slechtziende mensen niet mogen rijden, is het niet nodig de site ook voor blinde mensen accessible te maken.

- [performance-matters](https://github.com/roobinh/performance-matters-1819)

We gaan op onze site op zo veel mogelijk plekken rekening houden met de performance. Het gebruiken van een kaart en client-side javascript moet zo soepel mogelijk lopen, ook op langzaam internet.

- [real-time-web](https://github.com/roobinh/real-time-web-1819)

Als we real-time data van palen krijgen, gaan we dit in de kaart verwerken. Verder gaan we een chat maken waarbij sockets gebruikt worden. 


## 4. Reflectie (met rubric)
_In dit hoofdstuk reflecteer ik op mijn leerdoelen hierboven, met behulp van de rubrics._

**web-app-from-scratch**

<img src="https://i.ibb.co/cF39BhG/image.png">

**css-to-the-resque**

<img src="https://i.ibb.co/KWgvtY4/image.png">


**web-design**

Omdat er van web-design geen rubrik te vinden is, kan ik die helaas niet gebruiken. Wel hebben we rekening gehouden met veel mensen, veel getest en scoort onze site een 94 op accessability:

<img src="https://i.ibb.co/Mst3qbq/image.png">

**performance-matters**

Helaas werkt onze site niet zonder Javascript en hebben we geen tijd gehad voor een Service Worker. Wel wordt de website (met alle data) super snel server-side gerenderd en is er gebruik gemaakt van clusters om de performance drastisch te verbeteren.

**real-time-web**

<img src="https://i.ibb.co/KqVFt7j/image.png">

