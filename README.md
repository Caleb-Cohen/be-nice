# Dev Directory

This is a service for individuals who want to be nicer, built by [Caleb Cohen](https://caleb-cohen.com/).

## Developing

### Git Conventions

Branch names should be `#issuenumber-shortname` e.g. `#2-discord-auth` or `#4-profile-schema`.  If the branch is not associated with an issue, use your initials instead e.g. `tc-fix-profiles`.

### Prerequisites

1. Git
2. Node.js v18.14.0 or nvm
3. Docker (preferred) or a Postgres server

### Setup Instructions

1. Fork the project's repository on GitHub and clone it to your local system using Git.
   ```bash
   git clone git@github.com:github.com/Caleb-Cohen/be-nice.git
   ```
2. Navigate to the project directory in your terminal.
   ```bash
   cd dev-directory
   ```
3. If using Docker, ensure Docker is running and run dependencies
   ```bash
   npm run docker:deps
   ```
   > If you're NOT using Docker, be sure to start your Postgres server.
4. Ensure you are on Node v18.14.0. If you are using NVM, you can run the following to automatically use the correct version.
   ```bash
   nvm use
   ```
5. In a new terminal, run the command `npm ci` to install all the necessary packages for the project.
6. Copy `.env.sample` to `.env` and configure it for your local environment.
7. If it's your first time setting it up, run migrations:

```bash
npm run sequelize-cli db:migrate
```

If you get the error: 'password authentication failed for user "postgres"' while using docker, check to see if postgres is already installed on your computer. If it is, it may interfere with our default docker configuration. If you are familiar with port mapping, you can change the port postgres is using. Otherwise, uninstalling postgres will solve the error.

8. Run `npm run dev` to start the development server.

That's it! You're now ready to start working on the project.

> If using Docker, you can run `docker:db` to log into the Postgres CLI.

### Migrations

When making changes to a model's properties or the database schema, you'll need to create a migration. You can do so via the Sequelize CLI (replace `name-of-migration`):

```bash
npm run sequelize-cli migration:generate --name name-of-migration
```

Edit the migration - see more detailed docs [here](https://sequelize.org/docs/v6/other-topics/migrations/#migration-skeleton)

To run migrations:

```bash
npm run migrate
```

To rollback the last-run migration:

```bash
npm run migrate:undo
```

## Testing

We use [jest](https://jestjs.io/) for unit testing on the client and server, and for acceptance tests on the server in conjunction with [supertest](https://github.com/ladjs/supertest#readme).

Run all tests with:
```
npm run test
```

You can also run a single test:
```
npm run test -- auth.test.ts
```

You can run in [watch mode](https://jestjs.io/docs/cli#--watch), which is useful for when you're developing/writing the tests:
```
npm run test:watch -- auth.test.ts
```

### UI Testing

We use [cypress](https://docs.cypress.io/guides/overview/why-cypress) for UI testing. Cypress requires the app to be running for testing.

To get started,
* Execute `npm run cypress:open`
* Select E2E testing
* Choose a browser
* run the requisite test spec from the list.

## Deployment

We have a CI/CD pipeline that will automatically deploy to a staging app on Heroku. The flow is:
* A PR is merged to `main`
* Lint, Type Checking, and Tests are run
* If passed, heroku automatically builds the app:
  * By running `next build`, and
  * Compiling the server into JS
* Migrations are then run against the staging database
* This is then deployed onto a Basic Heroku dyno at the link below

If at any point any of these steps fail, it will not continue.

The [staging app (WIP)]() behaves exactly like the production app, but should not be used as the production app since data may be deleted from it at any point.

The production app will eventually require a manual deploy but staging can be used as a way to test features in a production environment using "production" data.

