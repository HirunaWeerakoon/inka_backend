# Inka Backend

Spring Boot backend for the Inka apparel application.

## Deployment

This app is prepared for Docker/VPS deployment with Java 21 and Postgres.

Required production variables are listed in `env.example`. Create real values on the server or in your process manager; do not commit `.env` files.

Build the application locally:

```sh
./mvnw test
./mvnw -q -DskipTests package
```

Build and run with Docker:

```sh
docker build -t inka-backend .
docker run --env-file .env -p 8080:8080 inka-backend
```

Run a local Postgres-backed stack:

```sh
docker compose up --build
```

Smoke checks after startup:

```sh
curl -i http://localhost:8080/api/products
curl -i http://localhost:8080/api/categories
curl -i http://localhost:8080/h2-console
```

In production, `/h2-console` should not be accessible. Stripe webhooks must be configured to send events to `/api/checkout/webhook/stripe` with the matching `STRIPE_WEBHOOK_SECRET`.

## Database

Production uses Postgres through:

```text
DATABASE_URL=jdbc:postgresql://host:5432/database
DATABASE_USERNAME=...
DATABASE_PASSWORD=...
```

Hibernate is currently configured with `spring.jpa.hibernate.ddl-auto=update` for initial deployment readiness. Use a migration tool such as Flyway before stricter production change control is needed.

## Secrets

Rotate any previously exposed Google OAuth, Cloudinary, Stripe, database, and JWT secrets in their provider dashboards. Generated build logs and local database files are ignored and should not be committed.
