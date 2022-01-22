# KEX-Antragsstatus-API

> ⚠️ You'll find German domain-specific terms in the documentation, for translations and further explanations please refer to our [glossary](https://docs.api.europace.de/common/glossary/)

## General

This API makes it possible to change the status of an application or to add additional information to the current status. The service expects a `POST` request with a JSON-document as request body.

> ⚠️ This API is continuously developed. Therefore we expect
> all users to align with the "[Tolerant Reader Pattern](https://martinfowler.com/bliki/TolerantReader.html)", which requires clients to be
> tolerant towards compatible API changes when reading and processing the data. This means:
>
> 1. unknown properties must not result in errors
>
> 2. Strings with a restricted set of values (Enums) must support new unknown values
>
> 3. sensible usage of HTTP status codes, even if they are not explicitly documented
>

<!-- https://opensource.zalando.com/restful-api-guidelines/#108 -->

## Statuswechsel

The Antragsstatus-API for **Kredit**Smart-Anträge can be accessed at the following URL:

```
https://www.europace2.de/kreditsmart/kex/antraege/status
```

The following properties are available for the request-body:

Request parameter            | Description | Comment
-----------------------------|-------------|--------
antragsnummer                | Identifier of the application on the Europace2 platform| Mandatory if no **produktanbieterantragsnummer** is submitted.
produktanbieterantragsnummer | Identifier of the application to the relevant Produktanbieter | Mandatory if no **antragsnummer** is submitted.
produktanbieterstatus        | Status of the application with the Produktanbieter | Mandatory, if no **antragstellerstatus** is submitted. Allowed values are: <ul><li><code>NICHT_BEARBEITET</code></li><li><code>UNTERSCHRIEBEN</code></li><li><code>ABGELEHNT</code></li><li><code>ZURUECKGESTELLT</code></li></ul>
antragstellerstatus          | Status of the application with the Antragsteller | Mandatory, if no **produktanbieterstatus** is submitted. Allowed values are: <ul><li><code>BEANTRAGT</code></li><li><code>UNTERSCHRIEBEN</code></li><li><code>NICHT_ANGENOMMEN</code></li><li><code>WIDERRUFEN</code></li></ul>
kommentar                    | Comment, that can be displayed in the GUI | Optional
hinweise                     | List of hint texts, that can be displayed in the GUI | Optional


The following HTTP-headers will be expected:

Header Parameter | Description                                               | Comment                              |
-----------------|-----------------------------------------------------------|--------------------------------------|
Content-Type     | Content type of the request body                          | Always need to be `application/json` |

In case of success the interface will respond with a HTTP-status `200`.

## Authentication

An authentication is required for each request. This API is secured by the OAuth 2.0 client credentials flow using the [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/). To use these APIs your OAuth2-Client needs the following scopes:

| Scope                          | Label in Partnermanagement            | Description                                                |
|--------------------------------|---------------------------------------|------------------------------|
| privatkredit:antrag:schreiben  | KreditSmart-Anträge anlegen/verändern | Scope for updating a Vorgang |

To request a bearer token via Authorization-API, this requires a client that has previously been created by an authorised person via Partnermanagement. For further details check out our [Help Center](https://europace2.zendesk.com/hc/de/articles/360012514780).

## HTTP-Status Errors

| Error Code | Message               | Description                     |
|------------|-----------------------|---------------------------------|
| 401        | Unauthorized          | Authentication failed           |
| 403        | Forbidden             | The API client misses a scope   |


## Examples

The examples shown here can be used for testing by `curl` in the following way:

```sh
curl -v -XPOST https://www.europace2.de/kreditsmart/kex/antraege/status \
	-H 'Accept: application/json' \
	-H 'Content-Type: application/json' \
	-H "Authorization: Bearer ${TOKEN}" \
	-d "${REQUEST_BODY}"
```

### Produktanbieterstatuswechsel with Produktanbieterantragsnummer

The Produktanbieterstatus for an application with the Produktanbieterantragsnummer `12919351` can be set to `UNTERSCHRIEBEN` with the following request-body:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "UNTERSCHRIEBEN"
}
```

### Produktanbieterstatuswechsel with Antragsnummer

Alternatively, the Antragsnummer can be transferred instead of the Produktanbieterantragsnummer:

```json
{
  "antragsnummer": "985132/1/1",
  "produktanbieterstatus": "UNTERSCHRIEBEN"
}
```

### Statuswechsel with comment

Furthermore, the status change can be provided with a comment which is displayed to the users of **Kredit**Smart in addition to the actual status change:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "ZURUECKGESTELLT",
  "kommentar": "Bitte noch eine Kopie des Personalausweises nachreichen."
}
```

If the Produktanbieterstatus already corresponds to the current status, the comment will still be added to the application.

### Statuswechsel with comment and hint texts

It is also possible to add a list of hint texts, which will then be displayed accordingly in **Kredit**Smart.

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

The Antragstellerstatuswechsel behaves analogous to the Produktanbieterstatus:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "antragstellerstatus": "UNTERSCHRIEBEN"
}
```

### Simultaneous change of Produktanbieter- and Antragstellerstatus

If necessary, both statuses can be changed at the same time:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "produktanbieterstatus": "UNTERSCHRIEBEN",
  "antragstellerstatus": "UNTERSCHRIEBEN"
}
```

## Terms of use
The APIs are made available under the following [Terms of Use](https://docs.api.europace.de/terms/).
