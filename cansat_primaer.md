# CanSat - Programmere primæroppdraget

### @diffs true
### @unifiedToolbox true

<!-- Del 1: -->

## CanSat primæroppdrag @unplugged

I denne veiledningen skal vi programmere primæroppdraget til CanSat. Når du er ferdig, vil du ha en fungerende CanSat som kan gjøre målingene vi trenger.

### **Det som må gjøres er: **

#### **1)** Bygge opp grunnstruktur på koden vår

#### **2)** Samle inn analogverdier fra NTC og vise på OLED-skjerm

#### **3)** Konvertere analogverdi fra NTC til spenningsverdi

#### **4)** Konvertere spenningsverdi fra NTC til lesbar temperaturverdi

#### **5)** Runde av verdiene våre for å ikke få for mange desimaler

#### **6)** Hente trykk-data fra BME280

#### **7)** Beregne CanSat'ens høyde over bakken 

#### **8)** Logge og lagre dataene på micro:bit

#### **9)** Overføre dataene fra CanSat til en PC, og vise de på skjermen


### **Lykke til!**


<!-- Del 1.1: -->

## Oppgave 1 - Bygge opp grunnstruktur på koden vår som gjør den lettere å lage
 
For å gjøre koden vår så lett som mulig å lage, bør vi starte med å strukturere koden vår.

#### Vi skal lage 4 ``||functions: funksjoner||`` i koden vår:

- En som samler data: f.eks. **samle_data**
- En for å vise dataene på OLED-skjerm: f.eks. **vise_data_OLED**
- En for å vise dataene på PC-skjerm: f.eks. **vise_data_PC**
- En som lar meg lagre dataene på internminne til micro:bit: f.eks. **lagre_data**

Alle funksjonene skal kjøres fra ``||basic: gjenta for alltid||``. Legg også inn en ``||basic: pause||`` på 500ms. etter at alle funksjonene har kjørt.

```blocks
function samle_data () {}
function vise_data_OLED () {}
function vise_data_PC () {}
function lagre_data () {}
basic.forever(function () {
    samle_data()
    vise_data_OLED()
    vise_data_PC()
    lagre_data()
    basic.pause(500)
})

```

<!-- Del 2.1: -->

## Oppgave 2 - Hente analogverdi fra temperatursensor (NTC)

For å måle temperaturen rundt CanSat, skal vi bruke en 10 kΩ NTC. Motstanden dens vil variere avhenging av temperaturen. Vi må derfor  regne om den analoge verdien vi får fra NTC'en, som er koblet til pin P10.

Lag en ny variabel ``||variables: analogverdi_NTC||``. Sett den til å lese av ``||pins: analogverdi fra P10||``. Gjør dette inne funsjonen: ``||functions: samle_data||``. 

```blocks
let analogverdi_NTC = 0
function samle_data () {
    analogverdi_NTC = pins.analogReadPin(AnalogReadWritePin.P10)
}
```

<!-- Del 2.2: -->

## Oppgave 2 - Vis tekst og verdier på OLED-skjerm @unplugged

Kitronik OLED-skjerm lar oss vise verdiene våre mye mer effektivt enn skjermen på micro:biten: 

For å ta i bruk OLED-skjermen, koble den til mellom CanSat og micro:bit.

![OLED_Kitronik_liten.jpg](https://i.postimg.cc/mD2ry8kJ/OLED_Kitronik_liten.jpg)


<!-- Del 2.3: -->

## Oppgave 2 - Sette opp OLED-skjerm for å kunne vise analogverdi fra NTC

Fra biblioteket ``||kitronik_VIEW128x64: OLED-skjerm||``, hent blokkene ``||kitronik_VIEW128x64: skru AV OLED-skjerm||`` og ``||kitronik_VIEW128x64: Sett font størrelse til Normal||``. 

Plasser begge blokkene inn i ``||basic: ved start||``, og sett OLED-skjerm til ``||kitronik_VIEW128x64: PÅ||``.

```blocks
kitronik_VIEW128x64.controlDisplayOnOff(kitronik_VIEW128x64.onOff(true))
kitronik_VIEW128x64.setFontSize(kitronik_VIEW128x64.FontSelection.Normal)
```

<!-- Del 2.4: -->

## Oppgave 2 - Vise analogverdi fra NTC på OLED-skjerm

Inni funksjonen ``||functions: vise_data_OLED||`` skal vi kjøre kodene for alt vi vil vise på OLED-skjermen vår. 

For å vise noe på skjermen, bruk blokken ``||kitronik_VIEW128x64: vis ||``. Trykk på pluss(+) for å utvide blokken, og bestemme hvilken linje teksten skal skrives på:

For å kunne vise både tekst og en variabel på samme linje, må vi hente ``||text: sett sammen ||`` fra biblioteket ``||text: Tekst ||``. Sett blokken inni ``||kitronik_VIEW128x64: vis ||``.

I den første ruta til ``||text: sett sammen ||``, skriv "Analog (NTC): ". I den andre ruta, sette inn variabelen ``||variables: analogverdi_NTC||``.

**Last ned koden på micro:bit på CanSat, og sjekk at du får verdien ut på OLED-skjermen.** Du kan forvente en analogverdi rundt 500.

```blocks
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
}
```

<!-- Del 2.5: -->

## Oppgave 2 - Oppdatere OLED-skjerm

Vi må sørge for at skjermen alltid er oppdatert og viser riktig tekst og verdier. Det gjør vi ved å bruke blokken ``||kitronik_VIEW128x64: fjern alt på skjermen ||``.

Plasser blokken øverst inni ``||basic: gjenta for alltid ||``.

```blocks
function samle_data () {
    analogverdi_NTC = pins.analogReadPin(AnalogReadWritePin.P10)
}
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
}
function lagre_data () {
	
}
function vise_data_PC () {
	
}
let analogverdi_NTC = 0
basic.forever(function () {
    kitronik_VIEW128x64.clear()
    samle_data()
    vise_data_OLED()
    vise_data_PC()
    lagre_data()
    basic.pause(500)
})
```


<!-- Del 3.1: -->

## Oppgave 3 - Konvertere analogverdi fra NTC til en lesbar temperaturverdi @unplugged

Den analoge verdien vi leser fra pin P10, skal vi regne om til en spenningsverdi, og så videre til en faktisk temperaturverdien. 

Vi må derfor lære hvordan man konverterer en analog verdi, som leses av micro:biten, til en spenningsverdi.

For å se at vi får riktige verdier i utregningene våre, skal vi koble til et AA-batteri til micro:biten og sjekke at formelen gir oss riktig spenning (1,5V). 

Koble pluss (rød ledning) til P0 og minus (sort ledning) til GND på CanSat.

![Oppkobling-AA-batteri-liten.jpg](https://i.postimg.cc/50fKhZh6/Oppkobling-AA-batteri-liten.jpg)

![batteriholder-1-5V.webp](https://i.postimg.cc/VkFHxBLV/batteriholder-1-5V.webp)


<!-- Del 3.2: -->

## Oppgave 3 - Lage en batteritester

Start med å lage en ny funksjon: ``||functions: batteritester||``. Denne funksjonen skal kun brukes i testfasen vår. Plasser den inn i ``||basic: gjenta for alltid||``. 

Inni ``||functions: batteritester||`` skal vi lage en ny variabel: ``||variables: analogverdi_batteri||``. Sett ``||variables: analogverdi_batteri||`` til å ``||pins: lese analog verdi fra P0||``.

```blocks
let analogverdi_batteri = 0
function batteritester () {
    analogverdi_batteri = pins.analogReadPin(AnalogPin.P0)
}
```

<!-- Del 3.3: -->

## Oppgave 3 - Lage en ny variabel: Uref

Spenningen som micro:bit leverer til sensorene vi bruker vil variere om den får strøm fra USB eller batteri.

Se tabell under for hva ``||variables: Uref||`` skal være :

|   **  Spenning fra USB  **   | |   **  Spenning fra batteri  **   |
| :------------: | | :------------: |
| 3.2 | | 3.0 |

Lage en ny variabel: ``||variables: Uref||``.

``||variables: Uref||`` skal settes inn i ``||basic: ved start||``, og sett den til verdien bestemt i tabellen over.


```blocks
kitronik_VIEW128x64.controlDisplayOnOff(kitronik_VIEW128x64.onOff(true))
kitronik_VIEW128x64.setFontSize(kitronik_VIEW128x64.FontSelection.Normal)
let Uref = 0
Uref = 3.2
```


<!-- Del 3.4: -->

## Oppgave 3 - Beregne spenningen til batteri og NTC

De lagrede analogverdiene til både batteriet og NTC'en skal bruk til å beregne spenningen deres. For å forstå formelen vi bruker, se i hint-boksen.

Lage to nye variabel: ``||variables: spenning_NTC||`` og ``||variables: spenning_batteri||``.

- Sett ``||variables: spenning_NTC||`` til ( ``||variables: analogverdi_NTC||`` / 1023 ) * ``||variables: Uref||``. (Plasseres i funksjonen ``||functions: samle_data||``)
- Sett ``||variables: spenning_batteri||`` til ( ``||variables: analogverdi_batteri||`` / 1023 ) * ``||variables: Uref||``. (Plasseres i funksjonen ``||functions: batteritester||``)

![Formel-spenning-fra-analogverdi-liten-kopi.png](https://i.postimg.cc/Nfqbf0g6/Formel-spenning-fra-analogverdi-liten-kopi.png)


```blocks
let analogverdi_batteri = 0
let analogverdi_NTC = 0
let spenning_batteri = 0
let spenning_NTC = 0
function samle_data () {
    analogverdi_NTC = pins.analogReadPin(AnalogPin.P10)
    spenning_NTC = analogverdi_NTC / 1023 * Uref
}
function batteritester () {
    analogverdi_batteri = pins.analogReadPin(AnalogPin.P0)
    spenning_batteri = analogverdi_batteri / 1023 * Uref
}
```


<!-- Del 3.5: -->

## Oppgave 3 - Vis spenningsverdi på OLED-skjerm

Vi skal gjøre det samme som vi gjorde for å vise analogverdien til NTC på OLED-skjermen. Husk å plassere det inn i funksjonen ``||functions: vise_data_OLED||``.

Hent to nye blokker av ``||kitronik_VIEW128x64: vis ||``. Plasser ``||text: sett sammen ||`` fra biblioteket ``||text: Tekst ||`` inni ``||kitronik_VIEW128x64: vis ||``. 

- Inni den første ``||kitronik_VIEW128x64: vis ||``: I den første ruta til ``||text: sett sammen ||``, skriv "Spenning (NTC): ". I den andre ruta, sette inn variabelen ``||variables: spenning_NTC||``. Utvid til en tredje rute hvor du skriver " V". Skriv dette på linje 2.
- Inni den andre ``||kitronik_VIEW128x64: vis ||``: I den første ruta til ``||text: sett sammen ||``, skriv "Spenning (batteri): ". I den andre ruta, sette inn variabelen ``||variables: spenning_batteri||``. Utvid til en tredje rute hvor du skriver " V". Skriv dette på linje 3.
- 

**Last ned koden på micro:bit på CanSat, og sjekk at du får verdien ut på OLED-skjermen.** Du kan forvente en spenning rundt 1,5V fra batteriet.

```blocks
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
    kitronik_VIEW128x64.show("Spenning (NTC): " + spenning_NTC + " V", 2)
    kitronik_VIEW128x64.show("Spenning (batteri): " + spenning_batteri + " V", 3)
}
```

<!-- Del 3.6: -->

## Oppgave 3 - Sjekke batteritester og rydde opp i koden

Hvis vi får en spenning på ca. 1,5V fra batteriet, kan vi konkludere med at utregningene våre er riktige. Vi kan derfor slette følgende kode:

- Hele funksjonen ``||functions: batteritester||``
- ``||kitronik_VIEW128x64: vis ||`` Spenning (batteri) fra funksjonen ``||functions: vise_data_OLED||``


<!-- Del 4.1: -->

## Oppgave 4 - Regne om NTC spenningsverdi til temperaturverdi

For å få så nøyaktig temperaturverdi fra NTC'en, må vi gjøre en kalibrering. Det skal vi gjøre etterpå. Så foreløpig skal vi bruke formelen under:

Inne ``||functions: samle_data||``, lage en ny variabel: ``||variables: temperatur_NTC||``.

Bruk denne formelen for å sette ``||variables: temperatur_NTC||`` til (39.7956 * ``||variables: spenning_NTC||``) - 42.7499

Se hint for bilde av formel:

![Formel-temperatur-NTC-fra-spenning-NTC.png](https://i.postimg.cc/MG6nhdGq/Formel-temperatur-NTC-fra-spenning-NTC.png)

```blocks
function samle_data () {
    analogverdi_NTC = pins.analogReadPin(AnalogPin.P10)
    spenning_NTC = analogverdi_NTC / 1023 * Uref
    temperatur_NTC = 39.7956 * spenning_NTC - 42.7499
}
```

<!-- Del 4.2: -->

## Oppgave 4 - Vise temperaturverdi fra NTC på OLED-skjerm

Plasseres inn i funksjonen ``||functions: vise_data_OLED||``:

Hent en ny blokk av ``||kitronik_VIEW128x64: vis ||``. Plasser ``||text: sett sammen ||`` fra biblioteket ``||text: Tekst ||`` inni ``||kitronik_VIEW128x64: vis ||``. 

 I den første ruta til ``||text: sett sammen ||``, skriv "Temperatur (NTC): ". I den andre ruta, sette inn variabelen ``||variables: temperatur_NTC||``. Utvid til en tredje rute hvor du skriver " C". Skriv dette på linje 3.

**Last ned koden på micro:bit på CanSat, og sjekk at du får verdien ut på OLED-skjermen.** Sjekk om temperaturverdien gir mening. Det gjør ikke noe om den ikke gjør det.


```blocks
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
    kitronik_VIEW128x64.show("Spenning (NTC): " + spenning_NTC + " V", 2)
    kitronik_VIEW128x64.show("Temperatur (NTC): " + temperatur_NTC + " C", 3)
}
```
<!-- Del 4.6: -->

## Oppgave 5 - Runde av verdier til 2 desimaler

Dere har sikkert lagt merke til at verdiene våre har mange desimaler. Vi har ingen blokk som lar oss direkte avrunde til 2 desimaler. Vi må derfor lage en funksjon som vi kan bruke for å avrunde de verdiene vi ønsker. 

Lag en ny funksjon: ``||functions: avrund||``. Legg til parameteret "nummer" på linja over når du lager funksjonen. Endre "tall" til "sensorverdi". Hent frem retur-blokken og sett den inn i funksjonen.

I returblokken skal vi multiplisere ``||variables: sensorverdien||`` med 100, og deretter ``||math: avrund ||`` dette. Det gir oss et heltall som er 100 ganger for stort. Hvis vi nå deler det nye tallet vårt på 100, vil vi få riktig antall desimaler.

Kjør funksjonen ``||functions: avrund||`` i blokken der vi viser både ``||variables: spenning_NTC||`` og ``||variables: temperatur_NTC||``. 

```blocks
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
    kitronik_VIEW128x64.show("Spenning (NTC): " + avrund(spenning_NTC) + " V", 2)
    kitronik_VIEW128x64.show("Temperatur (NTC): " + avrund(temperatur_NTC) + " C", 3)
}
function avrund (sensorverdi: number) {
    return Math.round(sensorverdi * 100) / 100
}
```
**Last ned koden på micro:bit på CanSat, og sjekk at verdiene på OLED-skjermen vises kun med 2 desimaler.**


<!-- Del 6: -->

## Oppgave 6 - Lag et barometer @unplugged

#### Vi skal bruke en ``||BME280: BME280||`` sensor. 

![BME280.png](https://i.postimg.cc/3J0ZjjvD/BME280.png)

Denne sensoren kan måle:

- Temperatur
- Trykk
- Luftfuktighet
- Duggpunkt

For å lage et barometer skal vi bruke blokken ``||BME280: trykk||``.


<!-- Del 6.1: -->

## Oppgave 6: Koble opp og lese av fra BME280

For å få BME280 til å snakke med CanSat, skal vi sette opp to blokken inn i ``||basic: ved start||`` fra biblioteket ``||BME280: BME280||``:

- ``||BME280: Skru PÅ||``
- ``||BME280: Sett adresse 0x76||``

Inne ``||functions: samle_data||``, sett opp en ny variabel: ``||variables: trykk||``. Sett ``||variables: trykk||`` til ``||BME280: trykk||`` (fra biblioteket BME280).

```blocks
kitronik_VIEW128x64.controlDisplayOnOff(kitronik_VIEW128x64.onOff(true))
kitronik_VIEW128x64.setFontSize(kitronik_VIEW128x64.FontSelection.Normal)
Uref = 3.2
BME280.PowerOn()
BME280.Address(BME280_I2C_ADDRESS.ADDR_0x76)
function samle_data () {
    analogverdi_NTC = pins.analogReadPin(AnalogPin.P10)
    spenning_NTC = analogverdi_NTC / 1023 * Uref
    temperatur_NTC = 39.7956 * spenning_NTC - 42.7499
    trykk = BME280.pressure(BME280_P.Pa)
}
```

<!-- Del 6.2: -->

## Oppgave 6: Skrive lufttrykk på OLED-skjerm

Kopier koden vi har brukt før for å skrive til OLED-skjerm inni ``||functions: vise_data_OLED||``

I den første ruta, skriv "Trykk: ". I den andre ruta, sette inn ``||variables: trykk||``. I den tredje, skriv " Pa". Skriv til linje 4.

**Last ned koden på micro:bit på CanSat, og sjekk at du får verdien ut på OLED-skjermen.** Forventet trykk ligger rundt 100 000 Pa.


```blocks
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
    kitronik_VIEW128x64.show("Spenning (NTC): " + avrund(spenning_NTC) + " V", 2)
    kitronik_VIEW128x64.show("Temperatur (NTC): " + avrund(temperatur_NTC) + " C", 3)
    kitronik_VIEW128x64.show("Trykk: " + trykk + " Pa", 4)
}
function avrund (sensorverdi: number) {
    return Math.round(sensorverdi * 100) / 100
}
```

<!-- Del 7: -->

## Oppgave 7 - Beregne høyden til CanSat over bakken @unplugged

For å beregne høyden CanSat har over bakken, skal vi bruke formelen under.

![Regne_ut_høyde_i_forhold_til_trykk_liten_200.png](https://i.postimg.cc/6pygnRB4/Regne_ut_høyde_i_forhold_til_trykk_liten_200.png)

- **h:**  Beregnet høyde i meter
- **h1:** Referansehøyde i meter (starthøyde er 0 eller m.o.h.)
- **T:**  Temperatur i Kelvin (``||variables: temperatur_NTC||`` + 273,15)
- **T1:** Referansetemperatur ved referansehøyden h1
- **R:**  Den spesifikke gasskonstant 287,06 J/kg K
- **a:**  Temperaturgradient, foreslått verdi -0,0065 K/m
- **p:**  Målt trykk i Pa
- **p1:** Trykk i Pa ved referansehøyden h1
- **g0:** Tyngdeakselerasjonen 9,81 m/s^2


<!-- Del 7.1: -->

## Oppgave 7 - Sette trykk og temperatur ved referansehøyden

Før vi kan bygge formelen må vi lage en måte å nullstille høydemåleren vår.

Lag to ny variabler: ``||variables: trykk_1||`` og ``||variables: temperatur_NTC_1||``. Plasser disse inn i ``||input: når knapp A trykkes||``.

- Sett ``||variables: trykk_1||`` til ``||variables: trykk||``
- Sett ``||variables: temperatur_NTC_1||`` til``||variables: temperatur_NTC||`` 

Dette vil gjøre at når knapp A trykkes inn, nullstilles høydemåleren.

```blocks
input.onButtonPressed(Button.A, function () {
	trykk_1 = trykk
    temperatur_NTC_1 = temperatur_NTC
})
```

<!-- Del 7.2: -->

## Oppgave 7 - Bygge formelen for å beregne høyden til CanSat

Vi har en egen blokk som gjør denne utregningen for oss.

Lag en ny variabel: ``||variables: høyde||``. Sett den inn i funksjonen ``||functions: samle_data||``.

Plasser blokken fra biblioteket ``||barometric-height: Høydeberegning||``.

Vi må plassere disse variablene inn i blokken:

- Inni ruten p: Plasser variabelen ``||variables: trykk||``
- Inni ruten p1: Plasser variabelen ``||variables: trykk_1||``
- Inni ruten T1: Plasser variabelen ``||variables: temperatur_NTC_1||`` ``||math: + 273,15||``

```blocks
let høyde = høydeberegning.barometricHeight(
trykk,
trykk_1,
temperatur_NTC_1,
0.0065,
0,
287,
9.81
)
```

<!-- Del 7.3: -->

## Oppgave 7 - Vise høyden til CanSat på OLEd-skjermen

Kopier koden vi har brukt før for å skrive til OLED-skjerm inni ``||functions: vise_data_OLED||``

I den første ruta, skriv "Høyde: ". I den andre ruta, sette inn ``||variables: høyde||``. I den tredje, skriv " m". Skriv til linje 5.

For å få 2 desimaler, kjør ``||functions: avrund||`` inni der vi skriver variabelen ``||variables: høyde||``.

**Last ned koden på micro:bit på CanSat, og sjekk at du får verdien ut på OLED-skjermen.** Sjekk om høydemålingen gir mening. Viktig å trykke på knapp A på micro:bit for nullstille høyden.

```blocks
function vise_data_OLED () {
    kitronik_VIEW128x64.show("Analog (NTC): " + analogverdi_NTC, 1)
    kitronik_VIEW128x64.show("Spenning (NTC): " + avrund(spenning_NTC) + " V", 2)
    kitronik_VIEW128x64.show("Temperatur (NTC): " + avrund(temperatur_NTC) + " C", 3)
    kitronik_VIEW128x64.show("Trykk: " + trykk + " Pa", 4)
    kitronik_VIEW128x64.show("Høyde: " + avrund(høyde) + " m", 5)
}
function avrund (sensorverdi: number) {
    return Math.round(sensorverdi * 100) / 100
}
```

<!-- Del 8.1: -->

## Oppgave 8 - Logge dataene vår på internminne til micro:bit @unplugged

![internminne_til_micro:bit](https://cdn.sanity.io/images/ajwvhvgo/production/759979022f4dd381418c793e73b9fea8a01deb26-800x423.png?q=95)

Alle dataene vi samler skal lagres i internminne til micro:bit.


<!-- Del 8.2: -->

## Oppgave 8 - Sette opp datalogger på micro:bit

Inni funksjonen ``||functions: lagre_data||``: Finn frem blokken ``||datalogger: logg data||`` fra biblioteket ``||datalogger: datalogger||``.

Her skal vi sette inn de forskjellige dataene vi har samlet. For å legge til flere kolonner, trykk på pluss(+)-knappen.

I kolonne, skriv hvilke data som samles. I verdi, plasser tilhørende variabel.

```blocks
function lagre_data () {
    datalogger.log(
    datalogger.createCV("Teller", teller),
    datalogger.createCV("Temperatur (NTC)", temperatur_NTC),
    datalogger.createCV("Trykk", trykk)
    )
}
``` 
<!-- Del 8.3: -->

## Oppgave 8 - Sjekke at data blir logget

**Last ned koden på CanSat, og koble den fra PC'en etterpå.**

La CanSat være skrudd på i 10-20 sekunder. Koble så micro:bit til PC'en.

Når Filutforsker åpner mappen til micro:bit, åpne filen: **MY_DATA**

Du bør nå se dataene dine her!


<!-- Del 9.1: -->

## Oppgave 9 - Overføre dataene fra CanSat til en PC, og vise de på skjermen

Vi skal nå sette opp det som skal inn i funksjonen ``||functions: vise_data_PC||``.

Finn frem blokken ``||serial: serieport skriv verdi||`` fra biblioteket ``||serial: serieport||``.

Her skal vi sette inn de forskjellige dataene vi har samlet. For å legge til en blokk for hver variabel du skal vise.

I stedet for "x", skriv hvilke data som samles. Og plasser tilhørende variabel inn der 0 står.

```blocks
function vise_data_PC () {
    serial.writeValue("Teller", teller)
    serial.writeValue("Temperatur (NTC)", temperatur_NTC)
    serial.writeValue("Trykk", trykk)
}
```

<!-- Del 10: -->

## Ferdig! 

Gratulerer! Du har nå en fungerende primær-oppdrag for CanSat med bruk av micro:bit!

```blocks
input.onPinPressed(TouchPin.P0, function () {

})
radio.onReceivedNumber(function (receivedNumber) {

})
input.onPinReleased(TouchPin.P0, function () {

})
input.onLogoEvent(TouchButtonEvent.Pressed, function () {

})
input.onButtonPressed(Button.A, function () {
    if (input.buttonIsPressed(Button.A)) {
        input.setAccelerometerRange(AcceleratorRange.OneG)
        input.setSoundThreshold(SoundThreshold.Loud, 128)
    } else if (input.acceleration(Dimension.X) == 0) {
        // skjerm blokker
        led.plot(led.brightness(), 0)
        led.toggle(led.pointBrightness(0, 0), 0)
        led.unplot(0, 0)
        led.plotBarGraph(
            0,
            0
        )
        led.plotBrightness(0, 0, 255)
        led.setBrightness(255)
        led.enable(false)
        led.stopAnimation()
        led.setDisplayMode(DisplayMode.BlackAndWhite)
    } else if (input.lightLevel() < 0) {
        // radio blokker
        radio.setGroup(1)
        radio.sendNumber(0)
        radio.sendValue("name", 0)
        radio.sendString("")
        radio.setTransmitPower(7)
        radio.setTransmitSerialNumber(true)
        radio.setFrequencyBand(0)
        radio.raiseEvent(
            EventBusSource.MICROBIT_ID_BUTTON_A,
            EventBusValue.MICROBIT_EVT_ANY
        )
    } else if (input.pinIsPressed(TouchPin.P0)) {

    } else if (input.isGesture(Gesture.Shake)) {

    } else if (input.compassHeading() == 0) {

    } else if (input.temperature() == 0) {

    } else if (input.logoIsPressed()) {

    } else if ("" == "") {

    } else if (input.soundLevel() < input.magneticForce(Dimension.X) && input.rotation(Rotation.Pitch) == input.runningTime()) {

    } else if (!(true) || false) {

    } else if (input.runningTimeMicros() == 0 || 0 == 0) {
        music.play(music.stringPlayable("- - - - - - - - ", 120), music.PlaybackMode.UntilDone)
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Whole)), music.PlaybackMode.UntilDone)
        music.ringTone(262)
        music.rest(music.beat(BeatFraction.Whole))
        music.setVolume(music.volume())
        music.stopAllSounds()
        music.changeTempoBy(music.beat(BeatFraction.Whole))
        music.setTempo(music.tempo())
        music._playDefaultBackground(music.builtInPlayableMelody(Melodies.Dadadadum), music.PlaybackMode.InBackground)
        music.stopMelody(MelodyStopOptions.All)
        music.play(music.builtinPlayableSoundEffect(soundExpression.giggle), music.PlaybackMode.UntilDone)
        music.play(music.createSoundExpression(WaveShape.Sine, 5000, 0, 255, 0, 500, SoundExpressionEffect.None, InterpolationCurve.Linear), music.PlaybackMode.UntilDone)
        music.setBuiltInSpeakerEnabled(false)
    } else if (music.isSoundPlaying()) {
        tekst = ("" + "this".split("") + String.fromCharCode(0).charCodeAt(0)).length
        tekst = parseFloat("123")
        tekst = convertToText(0).charAt(0).substr(0, "this".compare("")).indexOf("")
    } else if (led.point(0, 0)) {

    } else if (BME280.temperature() == BME280.pressure()) {
        BME280.PowerOn()
        BME280.PowerOff()
        BME280.Address(BME280_I2C_ADDRESS.ADDR_0x76)
    } else if (Math.randomBoolean()) {
        basic.showNumber(0 * 0 - Math.PI + 0 / (Math.min(Math.max(Math.sqrt(Math.round(randint(0, 10))), Math.abs(Math.constrain(0, 0, 0))), Math.map(0, 0, 1023, 0, 4)) % 1))
    } else if ("this".includes("")) {

    } else if ("this".isEmpty()) {

    } else if (pins.digitalReadPin(pins.map(
        0,
        0,
        1023,
        0,
        4
    )) == pins.analogReadPin(AnalogPin.P0)) {
        pins.digitalWritePin(DigitalPin.P0, 0)
        pins.analogWritePin(AnalogPin.P0, 1023)
        pins.analogSetPeriod(AnalogPin.P0, 20000)
        pins.setAudioPin(DigitalPin.P0)
        pins.setAudioPinEnabled(false)
        pins.servoWritePin(AnalogPin.P0, 180)
        pins.servoSetPulse(AnalogPin.P0, 1500)
    } else if (control.millis() == 0) {
        control.waitForEvent(control.eventValue(), 0)
        control.reset()
        control.waitMicros(control.eventTimestamp())
        control.raiseEvent(
            EventBusSource.MICROBIT_ID_BUTTON_A,
            EventBusValue.MICROBIT_EVT_ANY
        )
    } else {
        serial.writeLine(serial.readLine())
        serial.writeNumber(0)
        serial.writeValue("" + serial.readUntil(serial.delimiters(Delimiters.NewLine)) + serial.readString(), 0)
        serial.writeString("" + (serial.readBuffer(0)))
        serial.writeNumbers([0, 1])
        serial.redirect(
            SerialPin.P0,
            SerialPin.P1,
            BaudRate.BaudRate115200
        )
        serial.redirectToUSB()
        serial.setTxBufferSize(32)
        serial.setRxBufferSize(32)
        serial.setWriteLinePadding(0)
        serial.setBaudRate(BaudRate.BaudRate115200)
    }
})
input.onGesture(Gesture.Shake, function () {
    BME280.temperature()
    BME280.pressure()
    BME280.PowerOn()
    BME280.PowerOff()
    BME280.Address(BMP280_I2C_ADDRESS.ADDR_0x76)
})
function doSomething() {

}
radio.onReceivedString(function (receivedString) {

})
input.onSound(DetectedSound.Loud, function () {

})
radio.onReceivedValue(function (name, value) {

})
control.onEvent(EventBusSource.MICROBIT_ID_BUTTON_A, EventBusValue.MICROBIT_EVT_ANY, function () {

})
serial.onDataReceived(serial.delimiters(Delimiters.NewLine), function () {

})
pins.onPulsed(DigitalPin.P0, PulseValue.High, function () {

})
music.onEvent(MusicEvent.MelodyNotePlayed, function () {

})
let teksttabell: string[] = []
let tabell: number[] = []
let tekst = 0
// basis blokker
basic.showNumber(0)
basic.showLeds(`
    . . . . .
    . . . . .
    . . # . .
    . . . . .
    . . . . .
    `)
basic.showIcon(IconNames.Heart)
basic.showString("Hello!")
basic.clearScreen()
basic.pause(100)
basic.showArrow(ArrowNames.North)
radio.setGroup(1)
radio.sendNumber(0)
radio.sendValue("name", 0)
radio.sendString("")
radio.setTransmitPower(7)
radio.setTransmitSerialNumber(true)
radio.setFrequencyBand(0)
radio.raiseEvent(
    EventBusSource.MICROBIT_ID_BUTTON_A,
    EventBusValue.MICROBIT_EVT_ANY
)
loops.everyInterval(500, function () {

})
basic.forever(function () {
    let list: number[] = []
    for (let index = 0; index < 4; index++) {

    }
    // løkker blokker
    while (false) {

    }
    for (let verdi of tabell) {

    }
    for (let indeks = 0; indeks <= 4; indeks++) {

    }
    for (let index = 0; index < 5; index++) {

    }
    for (let index = 0; index < radio.receivedPacket(RadioPacketProperty.SignalStrength); index++) {
        continue;
        break;
    }
    // Logikk blokker
    if (true) {

    }
    if (true) {

    } else {

    }
    kitronik_VIEW128x64.controlDisplayOnOff(kitronik_VIEW128x64.onOff(false))
    kitronik_VIEW128x64.setFontSize(kitronik_VIEW128x64.FontSelection.Normal)
    kitronik_VIEW128x64.refresh()
    kitronik_VIEW128x64.invert(kitronik_VIEW128x64.onOff(false))
    kitronik_VIEW128x64.show(0)
    kitronik_VIEW128x64.setPixel(0, 0)
    kitronik_VIEW128x64.plot(0)
    kitronik_VIEW128x64.drawLine(kitronik_VIEW128x64.LineDirectionSelection.horizontal, 10, 0, 0)
    kitronik_VIEW128x64.drawRect(60, 30, 0, 0)
    kitronik_VIEW128x64.clearLine(1)
    kitronik_VIEW128x64.clearPixel(0, 0)
    kitronik_VIEW128x64.clear()
    // funksjoner blokker
    doSomething()
    // tabeller blokker
    tabell = [tabell.length, 2, 3]
    teksttabell = ["ei / en / ett", "b", "c"]
    tabell[list.removeAt(list._pickRandom())] = list.shift()
    tabell.push(tabell[list.pop()])
    tabell.pop()
    tabell = []
    tabell[0] = 0
    list.push(list.unshift(0))
    list.pop()
    tabell.shift()
    list.unshift(0)
    list.insertAt(0, list.indexOf(0))
    list.removeAt(0)
    list.reverse()
})
basic.forever(function () {

})
control.inBackground(function () {

})

datalogger.onLogFull(function () {
	
})
datalogger.log(datalogger.createCV("", 0))
datalogger.setColumnTitles("")
datalogger.deleteLog()
datalogger.includeTimestamp(FlashLogTimeStampFormat.None)
datalogger.mirrorToSerial(false)
datalogger.createCV("", null)
```

```package
oled-skjerm=github:oysa88/oled-skjerm
BME280=github:oysa88/BME280
barometric-height=github:oysa88/barometric-height
datalogger=github:oysa88/datalogger
```