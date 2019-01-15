# Integrasjonsguide

*Sist oppdatert 15.01.2019*

## Innholdsfortegnelse

[Meldingsformat](#meldingsformat)

[Kryptering](#kryptering)

## Meldingsformat
Beskrivelse av formatet på meldingene mellom som utveksles mellom Innsynsorkestratoren og registrene.

### Type meldinger

Det er p.t. seks dialogprosesser. Disse er hentet fra _Digital dialog - Implentasjonsguide standarder.docx_ som er skrevet av Direktoratet for eHelse. De fire siste må p.t. håndteres manuelt og trenger derfor ikke spesifiseres her.

1. **Dialog om oppføring:** Innbygger kan forespørre om han er registrert eller register kan fortelle innbygger at han er registrert.
2. **Dialog innsyn helseopplysninger:** Innsyn i opplysninger som er registrert om meg
3. **Dialog innsyn bruk:** Innsyn i hvem som har benyttet opplysninger registrert om meg
4. **Dialog innsyn utlevering:** Innsyn i hvem som har fått utlevert opplysninger om meg
5. **Dialog melde feil:** Melding om feil i opplysninger registrert om meg
6. **Dialog sletting eller sperring:** Anmodning om at opplysninger om meg slettes eller sperres for bruk

### Meldingsskjelett
Ambisjonen er at meldingen skal være så enkel som mulig og inneholde kun det som det absolutt er behov for.

#### Dialog om oppføring
Request JSON-objekt
```
{ 
    fodselsnummer: "12345678901"
}
```
- **fodselsnummer** (string): Fødselsnummer til aktuell innbygger

Response JSON-objekt
```
{
    oppforingsstatus: 0 | 1 | 2 
    statusTidsstempel: tidspunkt,
    dataSistEndret: tidspunkt
}
```
- **oppforingsstatus** (int): Er innbygger registrert i registeret?  
    0 – Ikke oppført  
    1 – Oppført  
    2 - Slettet
- **statusTidsstempel** (datetime): Skal angi tidsstempel for når oppføringsstatus ble kontrollert
- **dataSistEndret** (datetime): Kan benyttes for å informere om når de registrerte data sist ble endret i registeret

#### Dialog innsyn helseopplysninger
Request JSON-objekt 
```
{ 
    fodselsnummer: "12345678901"
    rapportHovedType: "STD" | "FULL" | "TRA" | "LOK"
    lokalRapportType: rapportens lokale identifikator
}
```

- **fodselsnummer** (string): Fødselsnummer til aktuell innbygger
- **rapportHovedType** (string): Hvilken rapport skal hentes.  
    STD – Standard rapport. (De viktigste opplysningene registrert om innbygger med forklaring slik at de er forståelige for innbygger).  
    FULL – Fullstendig rapport. (Alle opplysninger som er registrert om innbygger. Det kan være nødvendig å få hjelp av ekspertise for å tolke/forstå disse).  
    TRA – Transportabel rapport. (Alle registrerte data på en maskinlesbar form som gjør at kan transpores til annet register).  
    LOK – Lokalt definert registerrapport.
- **lokalRapportType** (string): Brukes bare hvis rapportHovedType er LOK. Identifikator til den lokale rapporten som skal hentes

Response JSON-objekt 
```
{
    innsyn: standard xml-format definert av e-helse
    stottedeRapporter: liste med støttede rapporter
    vedlegg: liste med vedlegg
}
```

- **innsyn** (string): Strukturert informasjon om bl.a. standard rapporten, støttede rapporter og vedlegg. XML-format definert i Innsyn-v6.01.xsd. Det er utviklet et eget testverktøy der registre kan teste hvordan en slik formatering vil bli på Helsenorge. Se digital dialog implementasjonsguiden til helsenorge.no kap 5.2.3 for mer informasjon. Denne er påkrevd for STD rapporten.
- **stottedeRapporter** (stottetRapport array): Alle rapporttyper utover STD som er støttet.
    stottetRapport:  
    ```
    {  
        rapportHovedType: "FULL" | "TRA" | "LOK"  
        lokalRapportType: rapportens lokale identifikator  
        lokalRapportBeskrivelse: rapportens lokale beskrivelse  
    }
    ```
- **vedlegg** (vedlegg array): 
    vedlegg:  
    ```
    {  
        mimetype:  
        innhold: Base64 kodet fil som er vedlegg til standardrapporten eller en annen rapport som vedlegg  
        innholdsbeskrivelse:  
    }
    ```

### Eksempler
#### Dialog om oppføring
Andreas Heimdal, fødselsnummer 01128330700, sender forespørsel til Tonsilleregisteret om han er registrert der. Det er han.

Request JSON-objekt 
```
{ 
    fodselsnummer: "01128330700"
}
```

Response JSON-objekt 
```
{
    oppforingsstatus: 1 
    statusTidsstempel: "2018-10-19T12:38:48",
    dataSistEndret: "2018-01-01T00:00:00"
}
```

#### Dialog innsyn helseopplysninger
Andreas Heimdal, fødselsnummer 01128330700, sender forespørsel til Tonsilleregisteret om å generere en Standard rapport til han. Han får den generert som strukturert informasjon samt en PDF-fil som vedlegg. Han får også informasjon om at FULL rapport er tilgjengelig i tillegg til STD.

Request JSON-objekt 
```
{ 
    fodselsnummer: "01128330700",
    rapportHovedType: "STD"
    lokalRapportType: ""
}
```


Response JSON-objekt 
```
{
    innsyn: "<XML_MED_STANDARD_RAPPORT>"
    stottedeRapporter: [{
        rapportHovedType: "FULL"
    }]
    vedlegg: [{  
        mimetype: "application/pdf"
        innhold: <BASE64_KODET_PDF_FIL>  
        innholdsbeskrivelse: "Vedlegg til Standardrapport"
    }]
}
```

## Kryptering
Kommunikasjon mellom Innsynsorkestrator og register krypteres både for å sikre at uvedkomne ikke skal kunne se dataene og for å sikre at det kommuniseres med korrekt register. Krypteringen baserer seg på en delt hemmelighet.

### Delt hemmelighet
Sikring av kommunikasjonen baseres på en hemmelig nøkkel som bare registeret og orkestratoren kjenner til. Denne er forskjellig for hvert enkelt register orkestratoren kommuniserer med og vil dermed også kunne brukes til å verifisere at meldingene går til / fra korrekt register. Nøkkelen blir generert av Innsynsorkestratoren og samme nøkkel må benyttes i registeret for å kunne dekryptere informasjonen. Nøkkelen er Base64 kodet.

Eksempel på nøkkel:
```
8HY69972SIS8lBodnWGwIva2HVwfn/BzldoecxKhOcU=
```

### Krypteringsalgoritme
Informsajon om krypteringsalgoritmen. Alle parametere er standard for c# implementasjonen av algoritmen.

```
Type: AES
BlockSize: 128
KeySize: 256
FeedbackSize: 8
Mode: CBC
Padding: PKCS7
```

### Flyt ved kryptering
Ved kryptering benyttes nøkkelen sammen med en tilfeldig generert initialiseringsvektor (IV) for å generere cipher-teksten som overføres mellom orkestrator og register. IV benyttes for å unngå at kryptering av to like tekster genererer samme byte-array. IV legges til på starten av det krypterte byte-arrayet som de første 16 bytene. Kombinasjonen av IV og det krypterte innholdet blir så base64-kodet til en streng før det overføres mellom orkestrator og register.

### Flyt ved dekryptering
Ved dekryptering går man motsatt vei. Den overførte strengen blir først konvertert fra base64-kodet streng til byte-array. De 16 første bytene gir IV, de resterende gir det krypterte innholdet som dekrypteres med nøkkelen og IV.

### Eksempel
Nøkkel (delt hemmelighet)
```
8HY69972SIS8lBodnWGwIva2HVwfn/BzldoecxKhOcU=
```

Data i klartekst
```
Text to be encrypted
```

Ved å bruke nøkkelen til å kryptere data i klartekst får man denne cipher-teksten
```
VxQSM09R+xJe+uDCpPcKHrzVk0iePxZzpPJMl2eb9wp2K15w0uocJS2UHlIXWP9H
```

Denne cipher-teksten kan brukes for å teste dekryptering ved å se om man får dekryptert tilbake til original tekst ved å benytte samme nøkkel
