Programowanie aplikacji w chmurze obliczeniowej
Zadanie 1 - Sprawozdanie
Autor: Szymon Kita

Część obowiązkowa:
Kod pliku app.js:
//Dołączenie potrzebnych bibliotek
const express = reqiure('express');
const requestIp = require('request-ip');
const geoip = require('geoip-lite');


const port = 3000


const app = express();
 app.get('/', (req, res) => {
    //Pozyskanie informacji o strefie czasowej i godzinie na podstawie IP klienta
    const ip = requestIp.getClientIp(req);
    const geo = geoip.lookup(ip);
    if(geo){
        const date = new Date();
        const time = date.toLocaleString('pl-PL', {timeZone: geo.timezone});


        res.send(`Adres ip: ${ip} <br/> Data i godzina strefy czasowej: ${time}`);
    }else{
        res.send(`Adres ip: ${ip} <br/> Nie udało się określić strefy czasowej`);
    }
 })


app.listen(port, () => `Serwer działa na porcie ${port}`);
Kod pliku Dockerfile
	#Utworzenie warstwy roboczej
FROM scratch as etap1


#Dołączenie warstwy bazowej
ADD alpine-minirootfs-3.19.1-x86_64.tar /


#aktualizacja i instalacja niezbędnych komponentów
RUN apk update && \
    apk upgrade && \
    apk add --no-cache nodejs=20.12.1-r0 \
    npm=10.2.5-r0 && \
    rm -rf /etc/apk/cache


#Zmiana katalogu
WORKDIR /home/node/app


#Kopiowanie plików aplikacji
COPY app.js ./app.js
COPY package.json ./package.json


#Etap 2
FROM node:iron-alpine3.19


#Zainstalowanie potrzebynch komponentów
RUN apk add --update --no-cache curl


#Utworzenie i zmiana folderu
RUN mkdir -p /home/node/app
WORKDIR /home/node/app


#Kopiowanie plików w etapu roboczego
COPY --from=etap1 /home/node/app/app.js ./app.js
COPY --from=etap1 /home/node/app/package.json ./package.json


#Informacje o porcie i autorze pliku
EXPOSE 3000
LABEL org.opencontainers.image.authors="Szymon Kita"


HEALTHCHECK --interval=4s --timeout=20s --start-period=2s --retries=3 \
    CMD curl -f http://localhost:3000/ || exit 1


#Zainstalowanie potrzebych komponentów
RUN npm install
RUN npm install express request-ip geoip-lite


ENTRYPOINT ["node", "app.js"]

Polecenia
Zbudowanie obrazu:
docker build -t local/zadanie1:v1 .
Uruchomienie kontenera na podstawie obrazu
docker run --name zadanie -p 3000:3000 local/zadanie1:v1
Informacje wygenerowane przez serwer
docker inspect local/zadanie1:v1
Sprawdzenie liczby warstw:
docker history local/zadanie1:v1
