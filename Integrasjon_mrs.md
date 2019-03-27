# Integrasjonsguide MRS
*Sist oppdatert 27.03.2019*

*NB:For å ta i bruk innsyn må registeret være på minimum hovedversjon 8 av kjernen.*

Innsynsfunksjonaliteten fra kjernen tilbyr for øyeblikket disse funksjonene:
1. **Oppføring:** Informasjon om en pasient er oppført i registeret. Dette gjør kjernen gratis for registeret.
2. **Helseopplysningsrapport:** Av de rapportene som er definert så tilbyr kjernen kun funksjonalitet for standardrapporten. Dersom man ønsker å legge til noen av de andre rapportene må hvert register gjøre det selv. Disse kan overrides fra kjernen.

## Standardrapport
Registeret må selv bestemme hvilke skjema og hvilke felt som skal tilgjengeliggjøres for innsyn i standardrapporten. 

Service-laget må lage sin implementasjon av HealthInformationService fra kjernen hvor skjema og felt kan legges til.

```
AddFormFieldsToDisplay((int)FormType.Hovedskjema, ConfigurationManager.AppSettings["RegisterHovedSkjemaFelt"]);
AddFormFieldsToDisplay((int)FormType.AnnetSkjema, ConfigurationManager.AppSettings["RegisterAnnetSkjemaFelt"]);
```
Her støttes en kommaseparert liste over feltnavn.

Datoformat kan angis etter feltnavnet separert med pipe. Feks:
```
CreationDate|dd.MM.yyyy, NextFieldName, AnotherFieldName
```

Registerimplementasjonen må registreres i service-laget.
```
containerBuilder.RegisterType<MyImplementationOfHealthInformationService>().As<IAccessToInformationService<InnsynHelseopplysningerRequest, InnsynHelseopplysningerResponse>>();
```

