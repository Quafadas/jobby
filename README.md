# Jobby

Not all the bits are currently in place. 


### Prerequisites

Just postgres.

Easiest way to run it is:

```
docker run -p 5432:5432 -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_DB=jobby -d postgres
```

Take note of the password (`mysecretpassword`) and database (`jobby`), you will need to configure the app .

### Running

To just run the app (not great for development per se),
run the following command:

```
sbt 'app/run 9999'
```

Then open http://localhost:9999 - and you should see a (not working) app.

### Configuration 

The following settings are respected, and can be either be set as environment variables (recommended for 
deployments), or read from the `jobby.opts` file at the root of the project, for example:

```properties
PG_PASSWORD=mysecretpassword
PG_DB=jobby
LOCAL_DEPLOYMENT=true
```

* `PG_PASSWORD`, `PG_HOST`, `PG_DB`, `PG_USER`, `PG_PORT` - variables to configure access to Postgres

* `LOCAL_DEPLOYMENT` - setting this to `true` will **disable** hardened cookies for authentication, making 
    it easier to develop locally

* `RELEASE` - setting it to `yesh` will produce fully optimised, single-file frontend from Scala.js


### Developing

This gets finicky as we try to satisfy the following requirements:

1. Backend should be rebuilt and restarted independently of the frontend
2. Frontend should be rebuilt on a per-module basis, and managed by https://vitejs.dev/
3. Request to backend should be proxied

To achieve (1), we need to start the backend without bundling the frontend - removing 
the `RELEASE` env variable will achieve that:

(in terminal 1)
```
PG_PASSWORD=mysecretpassword PG_DB=jobby sbt '~app/reStart 9999'
```

To achieve (2), we need to 

a) Continuously rebuilt the frontend:

   (in terminal 2)
   ```
   sbt '~buildFrontend'
   ```

b) Run Vite's development server:

   (in terminal 3)
   ```
   cd frontend-build && npm run dev
   ```

And (3) is achieved by the configuration in `frontend-build/vite.config.js`. **Make sure to start 
the server on port 9999**
