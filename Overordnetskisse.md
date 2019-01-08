# Overordnet skisse

![Overordnet skisse](img/overordnetskisse.png "Overordnet skisse")
| Komponent | Funksjon | Kommentar |
|---|---|---|
| Helsenorge | Portalen der innbyggere ved pålogging får tilgang til innsynsfunksjonalitet.	Lagrer informasjon om hvor en innbygger er oppført i innbyggers PHA. | Lagrer også svaret fra registeret på innbyggers innsynsforespørsel. |
| OFR Public del | Den delen av Oppføringsregisteret som inneholder metadata om alle Helseregistre der innbygger kan være registrert. | Benyttes av Helsenorge for å vise generell informasjon om hvert av de registre der innbygger er registrert. Benyttes også for å finne et registers HERID som er «lenken» til Adresseregistret der støttede elektroniske funksjoner for registeret er angitt. |
| Innsyns-orkestrator | Orkestrerer alle asynkrone forespørsler fra Helsenorge inn mot de nasjonale kvalitetsregister som driftes på de to register plattformene. | Utvikles av HEMIT som en fellesfunksjon både mot MRS og OpenQReg. |
| MRS og OpenQReg | Register plattformer der mange av de nasjonale kvalitetsregistrene driftes. |
