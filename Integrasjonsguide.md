# Integrasjonsguide

*Sist oppdatert 04.01.2019*

## Kryptering
Kommunikasjon mellom Innsynsorkestrator og register krypteres både for å sikre at uvedkomne ikke skal kunne se dataene og for å sikre at det kommuniseres med korrekt register. Krypteringen baserer seg på en delt hemmelighet.

### Delt hemmelighet
Sikring av kommunikasjonen baseres på en hemmelig nøkkel som bare registeret og orkestratoren kjenner til. Denne er forskjellig for hvert enkelt register orkestratoren kommuniserer med og vil dermed også kunne brukes til å verifisere at meldingene går til / fra korrekt register. Nøkkelen blir generert av Innsynsorkestratoren og samme nøkkel må benyttes i registeret for å kunne dekryptere informasjonen. Nøkkelen er Base64 kodet.

Eksempel på nøkkel:
8HY69972SIS8lBodnWGwIva2HVwfn/BzldoecxKhOcU=

### Krypteringsalgoritme
Informsajon om krypteringsalgoritmen. Alle parametere er standard for c# implementasjonen av algoritmen:

Type: AES
BlockSize: 128
KeySize: 256
FeedbackSize: 8
Mode: CBC
Padding: PKCS7

### Flyt ved kryptering
Ved kryptering benyttes nøkkelen sammen med en tilfeldig generert initialiseringsvektor (IV) for å generere cipher-teksten som overføres mellom orkestrator og register. IV benyttes for å unngå at kryptering av to like tekster genererer samme byte-array. IV legges til på starten av det krypterte byte-arrayet som de første 16 bytene. Kombinasjonen av IV og det krypterte innholdet blir så base64-kodet til en streng før det overføres mellom orkestrator og register.

### Flyt ved dekryptering
Ved dekryptering går man motsatt vei. Den overførte strengen blir først konvertert fra base64-kodet streng til byte-array. De 16 første bytene gir IV, de resterende gir det krypterte innholdet som dekrypteres med nøkkelen og IV.

### Eksempel
Nøkkel (delt hemmelighet)
´8HY69972SIS8lBodnWGwIva2HVwfn/BzldoecxKhOcU=´

Data i klartekst
´Text to be encrypted´

Ved å bruke nøkkelen til å kryptere data i klartekst får man denne cipher-teksten
´VxQSM09R+xJe+uDCpPcKHrzVk0iePxZzpPJMl2eb9wp2K15w0uocJS2UHlIXWP9H´

Denne cipher-teksten kan brukes for å teste dekryptering ved å se om man får dekryptert tilbake til original tekst ved å benytte samme nøkkel
