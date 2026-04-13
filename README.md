# Microservices Demo

Stick Martinez
Daniel Gonzalez

A demo application with Java, Go, Javascript, Kafka and PostgresQL.

## Architecture

![Architecture diagram](architecture.png)

- A front-end web app in [Java](/vote) which lets you vote between Tacos and Burritos
- A [Kafka](https://bitnami.com/stack/kafka/helm) queue which collects new votes
- A [Golang](/worker) or worker which consumes votes from Kafka and stores them in PostgresQL
- A [PostgresQL](https://bitnami.com/stack/postgresql/helm) database
- A [Node.js](/result) webapp which shows the results of the voting in real time

## Run the demo application in Okteto

```
git clone https://github.com/okteto/microservices-demo
cd microservices-demo
okteto login
okteto deploy
```

## Develop on the Result microservice

```
okteto up result
```

## Develop on the Vote microservice

```
okteto up vote
```

## Develop on the Worker microservice

```
okteto up worker
make start
```

## Notes

The voting application only accepts one vote per client. It does not register votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Okteto.

## CI/CD (GitHub Actions)

- `CI` (`.github/workflows/ci.yml`)
  - Se ejecuta en PR hacia `staging` y `main`.
  - Valida build/test de `vote` y `worker`.
  - Valida dependencias de `result`.
  - Construye imágenes Docker para `vote`, `worker` y `result`.

- `CD Images` (`.github/workflows/cd-images.yml`)
  - Se ejecuta en push a `staging` y `main`.
  - Construye y publica imágenes en ECR.
  - Publica un artifact con los tags generados (`image-tags.env`).

Secrets requeridos:

- `AWS_ROLE_TO_ASSUME`
