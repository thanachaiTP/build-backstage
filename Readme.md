# Backstage docker

- [Backstage.io](https://backstage.io)
- [Getting Started](https://backstage.io/docs/getting-started/)
- [Running the Backstage service catalog with Docker Compose](https://roadie.io/blog/backstage-docker-service-catalog/)

## 1. Run docker container nodejs
```
docker run --rm -it --name nodejs -v $PWD/app:/app -v /var/run/docker.sock:/var/run/docker.sock node:16-alpine sh

cd /app

apk add docker curl git wget
```

## 2. Create your Backstage App by roadie.io
```
>> npx @backstage/create-app --version 0.3.2
>> npx @backstage/create-app
? Enter a name for the app [required] scaffolded-app

cd scaffolded-app
yarn install --frozen-lockfile
yarn tsc
yarn build:backend
yarn workspace backend build-image
```

## 3. Docker-compose file
```
version: '3'
services:
  backstage:
    image: backstage
    environment:
      # This value must match the name of the postgres configuration block.
      POSTGRES_HOST: db
      POSTGRES_USER: postgres
    ports:
      - '7000:7000'

  db:
    image: postgres
    restart: always
    environment:
	# NOT RECOMMENDED for a production environment. Trusts all incomming
      # connections.
      POSTGRES_HOST_AUTH_METHOD: trust
```


```
docker-compose up
```


## 4. Configuring GitHub
```
version: '3'
services:
  backstage:
    container_name: backstage
    image: backstage
    restart: always
    environment:
      POSTGRES_HOST: db
      POSTGRES_USER: postgres
      # Add your token here
      GITHUB_TOKEN: <your-github-token>
    ports:
      - '7000:7000'

  db:
    image: postgres
    container_name: pg
    restart: always
    environment:
      POSTGRES_HOST_AUTH_METHOD: trust
```

```
docker-compose up
```

See Backstage on Browser `localhost:7000`

## 5. Making a change
edit `app-config.yaml`
```
organization:
  name: My Company
```

After change rebuild docker images and run `docker-compose up` again and refresh browser


## 6. Build Backstage on Host
```
docker run --rm -it --name nodejs -v $PWD/app:/app -v /var/run/docker.sock:/var/run/docker.sock node:16-alpine sh

cd /app

npx @backstage/create-app@latest
? Enter a name for the app [required] my-backstage-app

```
Finish and Exit container

## 7. Docker Build
```
docker image build . -f packages/backend/Dockerfile --tag backstage
```

Try out the image locally you can run the following:
```
docker run -it -p 7007:7007 backstage
```

See Backstage on Browser `localhost:7007`


