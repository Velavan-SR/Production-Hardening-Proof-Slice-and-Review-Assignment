# Production hardening proof slice

## Risk
Repeated provider failures can pile up doomed calls and spread an outage through the reply path.

## Control
`src/circuitBreaker.js` implements a circuit breaker around the model provider, with a safe fallback in `src/replyService.js`.

## Run / test
Run `node --test` from this directory; the expected result is all tests passing.

## Evidence
The automated tests prove threshold-based opening, dependency-free short-circuiting, one recovery probe, and failed-probe re-opening.

## Observability
`metrics.breaker_open_total` and `metrics.short_circuited_total` count control actions; `onStateChange` receives `OPEN`, `HALF_OPEN`, and `CLOSED`.

## Trade-off
The breaker can temporarily serve a fallback while the provider may have recovered, and it adds state and timing configuration.

## Remaining risk
This slice does not prevent failures inside the provider or guarantee that the fallback is delivered if the application itself is unavailable.

## Design link
It protects the Modules 1–3 decision to isolate external model-provider calls behind a resilient reply service.
