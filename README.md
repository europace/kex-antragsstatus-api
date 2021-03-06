# KEX-Antragsstatus-API

Die Statuswechsel API für **Kredit**Smart-Anträge ist unter folgender URL erreichbar:

```
https://www.europace2.de/kreditsmart/kex/antraege/status
```

## Statuswechsel

Diese Schnittstelle ermöglicht es, den Status eines Antrags zu verändern oder den aktuellen Status um Zusatzinformationen zu ergänzen. Der Service erwartet einen `POST`-Request mit einem JSON-Dokument als Request-Body.

Folgende Properties stehen für den Request-Body zur Verfügung:

Request Parameter            | Beschreibung | Anmerkungen
-----------------------------|--------------|-------
antragsnummer                | Kennung des Antrags auf der Europace2-Plattform | Pflichtangabe, sofern keine **produktanbieterantragsnummer** übermittelt wird.
produktanbieterantragsnummer | Kennung des Antrags beim zuständigen Produktanbieter | Pflichtangabe, sofern keine **antragsnummer** übermittelt wird.
produktanbieterstatus        | Status des Antrags beim Produktanbieter | Pflichtangabe, sofern kein **antragstellerstatus** übermittelt wird. Erlaubte Werte sind: <ul><li><code>NICHT_BEARBEITET</code></li><li><code>UNTERSCHRIEBEN</code></li><li><code>ABGELEHNT</code></li><li><code>ZURUECKGESTELLT</code></li></ul>
antragstellerstatus          | Status des Antrags beim Antragsteller | Pflichtangabe, sofern kein **produktanbieterstatus** übermittelt wird. Erlaubte Werte sind: <ul><li><code>BEANTRAGT</code></li><li><code>UNTERSCHRIEBEN</code></li><li><code>NICHT_ANGENOMMEN</code></li><li><code>WIDERRUFEN</code></li></ul>
kommentar                    | Kommentar zur Anzeige in der Benutzeroberfläche | Optional
hinweise                     | Liste von Hinweistexten zur Anzeige in der Benutzeroberfläche | Optional


Folgende HTTP-Header werden erwartet:

Header Parameter | Beschreibung                                               | Anmerkungen                                |
-----------------|------------------------------------------------------------|--------------------------------------------|
Content-Type     | Content Type des Request Bodies                            | Muss derzeit immer `application/json` sein |

Im Erfolgsfall gibt die Schnittstelle HTTP-Status `200` zurück.

## Authentifizierung

Für jeden Request ist eine Authentifizierung erforderlich. Die Authentifizierung erfolgt über den OAuth 2.0 Client-Credentials Flow.

| Request Header Name | Beschreibung           |
|---------------------|------------------------|
| Authorization       | OAuth 2.0 Bearer Token |


Das Bearer Token kann über die [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/) angefordert werden.
Dazu wird ein Client benötigt, der vorher von einer berechtigten Person über das Partnermanagement angelegt wurde.
Eine Anleitung dafür befindet sich im [Help Center](https://europace2.zendesk.com/hc/de/articles/360012514780).

Damit der Client für diese API genutzt werden kann, muss im Partnermanagement die Berechtigung **KreditSmart-Anträge anlegen/verändern** (Scope `privatkredit:antrag:schreiben`) aktiviert sein.  

Schlägt die Authentifizierung fehl, erhält der Aufrufer eine HTTP Response mit Statuscode **401 UNAUTHORIZED**.

Hat der Client keine Berechtigung die Resource abzurufen oder zu ändern, erhält der Aufrufer eine HTTP Response mit Statuscode **403 FORBIDDEN**.


## Beispiele

Die hier gezeigten Beispiele können zum Testen per `curl` auf folgende Art nachvollzogen werden:

```sh
curl -v -XPOST https://www.europace2.de/kreditsmart/kex/antraege/status \
	-H 'Accept: application/json' \
	-H 'Content-Type: application/json' \
	-H "Authorization: Bearer ${TOKEN}" \
	-d "${REQUEST_BODY}"
```

### Produktanbieterstatuswechsel mit Produktanbieterantragsnummer

Der Produktanbieterstatus für einen Antrag mit der Produktanbieterantragsnummer `12919351` kann mit folgendem Request-Body auf `UNTERSCHRIEBEN` gesetzt werden:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "UNTERSCHRIEBEN"
}
```

### Produktanbieterstatuswechsel mit Antragsnummer

Alternativ kann statt der Produktanbieterantragsnummer auch die Antragsnummer übergeben werden:

```json
{
  "antragsnummer": "985132/1/1",
  "produktanbieterstatus": "UNTERSCHRIEBEN"
}
```

### Statuswechsel mit Kommentar

Der Statuswechsel kann darüber hinaus mit einem Kommentar versehen werden, der den Anwendern von **Kredit**Smart zusätzlich zum eigentlichen Statuswechsel angezeigt wird:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "ZURUECKGESTELLT",
  "kommentar": "Bitte noch eine Kopie des Personalausweises nachreichen."
}
```

Sollte der Produktanbieterstatus schon dem aktuellen Status entsprechen, wird der Kommentar dennoch dem Antrag hinzugefügt.

### Statuswechsel mit Kommentar und Hinweistexten

Es ist außerdem möglich, eine Liste von Hinweistexten hinzuzufügen, welche dann in **Kredit**Smart entsprechend dargestellt wird.

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "ZURUECKGESTELLT",
  "kommentar": "Bitte reichen Sie noch folgende Dokumente nach:",
  "hinweise": [
  	"Personalausweis",
  	"Geburtsurkunde"
  ]
}
```

### Antragstellerstatuswechsel

Der Antragstellerstatuswechsel verhält sich analog zum Produktanbieterstatus:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "antragstellerstatus": "UNTERSCHRIEBEN"
}
```

### Gleichzeitiger Wechsel von Produktanbieter- und Antragstellerstatus

Bei Bedarf können auch beide Status gleichzeitig geändert werden:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "UNTERSCHRIEBEN",
  "antragstellerstatus": "UNTERSCHRIEBEN"
}
```

## Nutzungsbedingungen
Die APIs werden unter folgenden [Nutzungsbedingungen](https://docs.api.europace.de/nutzungsbedingungen/) zur Verfügung gestellt.
