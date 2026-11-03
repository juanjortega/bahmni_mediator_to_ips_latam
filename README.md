# bahmni_mediator_to_ips_latam

configuracion de channel y routes

A. Canal para ITI Mediator (iti-mediator)
1. Ve a “Channels” en OpenHIM Console
Haz clic en el botón verde “+ Channel”.

2. Completa los datos:
Channel Name:
ITI Event Channel

Channel URL Pattern:
^/event$

Allow:
admin (deja solo este grupo para control total)

Methods:
POST
(marca solo POST)

Type:
http

Routes (Add Route):

Route Name:
ITI Event Route

Host:
Si usas Docker Compose:

iti-mediator (nombre del servicio en docker-compose)
Si accedes directo desde host (no Docker):

localhost o la IP del host donde corre el mediador

Port:
5000

Path:
/

Primary:
Sí (marcado)

Type:
http

Endpoints:

Puedes dejarlo en blanco (solo para monitoreo avanzado).

Save/Crear

B. Canal para FHIR Proxy Mediator (proxy-mediator)
1. Ve a “Channels” en OpenHIM Console
Haz clic en “+ Channel” de nuevo.

2. Completa los datos:
Channel Name:
FHIR Proxy Channel

Channel URL Pattern:
^/fhir/.*$
(esto permite proxy universal de todo /fhir/*)

Allow:
admin

Methods:
Marca todos los necesarios:

GET, POST, PUT, PATCH, DELETE

Type:
http

Routes (Add Route):

Route Name:
FHIR Proxy Route

Host:
Si usas Docker Compose:

proxy-mediator
Si es fuera de Docker:

localhost o la IP del host

Port:
7000

Path:
/

Primary:
Sí

Type:
http

Endpoints:

Deja vacío (opcional).

Save/Crear

## Condiciones que van al IPS

El iti-mediator pide `Condition?patient=<id>&category=problem-list-item`: solo la lista de problemas del paciente (tabla `conditions` de OpenMRS) entra en la sección de problemas del IPS.

Los diagnósticos de cada consulta (`category=encounter-diagnosis`, que expone `openmrs-module-fhir-diagnosticos` y el EncounterBundle de Bahmni v2) **no** se envían. Son lo que se registró en un encuentro, no lo que el paciente «tiene», y meterlos como problemas llenaría el IPS de diagnósticos provisionales, revisados o descartados. Si más adelante se quieren en el IPS, deben ir en otra sección o con su categoría, no mezclados con los problemas.

Nunca pedir `Condition?patient=` sin `category`: con el módulo de diagnósticos instalado, esa consulta devuelve ambas categorías.
