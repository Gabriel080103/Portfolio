---

title: "Arbejde med Open Food Facts API og asynkrone API-kald"
description: "Jeg har arbejdet videre med integrationen af et eksternt API til fitness appen og undersøgt håndtering af API-kald med Callable og Future."
date: 2026-09-11
draft: false
tags:

- API
- Open Food Facts
- DTO
- Callable
- Future
- Kalorie tracking

---

I dag har jeg arbejdet videre med API-delen af mit fitness app projekt. Jeg har valgt at bruge Open Food Facts API'et til at hente information om forskellige fødevarer, som senere kan bruges til blandt andet kalorie- og makrotracking.

Jeg har arbejdet med at sende søgeforespørgsler til API'et og behandle det JSON-svar, jeg får tilbage. Jeg har blandt andet testet søgninger på produkter som "Chicken" og "Gifflar".

## Hvad har jeg lavet?

Jeg har lavet en `FoodAPI`-klasse, som står for kommunikationen med Open Food Facts. Klassen sender HTTP requests og bruger Jackson til at konvertere JSON-data fra API'et til mine egne Java-objekter.

Jeg har også lavet en `FoodDTO`, som indeholder de oplysninger, jeg har brug for fra API'et, blandt andet:

* Produktnavn
* Brand
* Stregkode
* Mængde
* Produktbillede
* Kalorier
* Protein
* Fedt
* Mættet fedt
* Kulhydrater
* Sukker
* Fibre
* Salt

På den måde behøver resten af programmet ikke arbejde direkte med Open Food Facts' JSON-struktur.

## Søgning efter produkter

Jeg har arbejdet med API'ets søgefunktion, så brugeren kan skrive et produktnavn og få relevante produkter tilbage.

Jeg fandt ud af, at API'et ikke altid returnerer det mest relevante produkt som det første resultat. Hvis jeg eksempelvis søger efter "Chicken", kan API'et returnere mange forskellige produkter, som ikke nødvendigvis er kylling.

For at løse dette henter jeg derfor flere resultater og laver en simpel scoring af dem. Scoren bruges til at finde det produkt, der bedst matcher brugerens søgning.

Jeg har også arbejdet med at gøre søgningen mere robust, så eksempelvis "ChickenBreast" bedre kan matche produkter, der hedder "Chicken Breast".

## Håndtering af fejl

Under arbejdet oplevede jeg også, at Open Food Facts nogle gange returnerer HTTP-status `503`, hvilket betyder, at API'et midlertidigt ikke er tilgængeligt.

Jeg har derfor implementeret retry-logik, så programmet forsøger igen, hvis API'et midlertidigt fejler.

Jeg fandt også ud af, at nogle produkter ikke indeholder næringsdata. Derfor skal programmet kunne håndtere, at `nutriments` er `null`, uden at programmet crasher med en `NullPointerException`.

## Callable og Future

Jeg har i dag også undersøgt, hvordan `Callable` og `Future` kan bruges til API-kald.

Jeg har oprettet en `FoodSearchService`, som håndterer den asynkrone del af søgningen. På den måde er `FoodAPI` stadig kun ansvarlig for selve kommunikationen med Open Food Facts, mens `FoodSearchService` håndterer udførelsen af søgningen.

Strukturen er derfor blevet:

text
Main
 ↓
FoodSearchService
 ↓
Callable / Future
 ↓
FoodAPI
 ↓
Open Food Facts API
 ↓
FoodDTO


Jeg har valgt at gøre dette nu, selvom frontend først kommer senere, fordi det gør projektet klarere opdelt og giver mulighed for senere at lave API-kald uden at blokere frontend.

## Hvad har jeg lært?

I dag har jeg især fået en bedre forståelse for, hvordan et eksternt API kan integreres i et Java-projekt.

Jeg har blandt andet arbejdet med:

* HTTP requests med `HttpClient`
* URL encoding
* JSON parsing med Jackson
* DTO'er til API-data
* Håndtering af HTTP-fejl
* Retry-logik
* Produkt-søgning og scoring
* `Callable`
* `Future`
* `ExecutorService`
* Håndtering af manglende data

Jeg har også fået en bedre forståelse for, at API-data ikke altid er komplette eller præcis som forventet, og at programmet derfor skal kunne håndtere manglende værdier og fejl fra API'et.

## Hvad skal jeg arbejde med næste gang?

* Få søgningen efter fødevarer til at give endnu mere relevante resultater
* Fortsætte arbejdet med `FoodSearchService`
* Få API-resultaterne integreret bedre med resten af projektet
* Forberede API-delen til den kommende frontend
* Arbejde videre med kalorie- og makrotracking
* Fortsætte med JPA, DAOs og DTO'er
