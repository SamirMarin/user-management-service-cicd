# user-management-service
User management service, manages creation and querying of user accounts.

## Running the service locally
1. Start local running dynamodb
```bash
# must be running docker to run this command
docker-compose up
```
2. Build the service
```bash
go build -o user-management-service
```

3. Run the service
```bash
./user-management-service
```

## Making requests to the service locally
1. Create a user
```bash
curl -X POST http://localhost:1324/create \
-H "Content-Type: application/json" \
-d '{
      "username": "samir@gmail.com",
      "email": "samir@gmail.com",
      "firstname": "Samir",
      "lastname": "Marin",
      "dob": "1989-01-01T00:00:00Z",
      "membership": {
          "kind": "unlimited",
          "owner": true,
          "joined": "2023-01-01T00:00:00Z",
          "renewal": "2024-01-01T00:00:00Z"
      }
    }'
```
2. Get a workout
```bash
curl -X POST http://localhost:1324/get \
-H "Content-Type: application/json" \
-d '{
  "username": "samir@gmail.com"
}'
```

## Deploying the service

1. Validate by running unit test

get a local version of the DB up and running

```
export AWS_ACCESS_KEY_ID=test
export AWS_SECRET_ACCESS_KEY=test
export AWS_DEFAULT_REGION='us-west-2'
export DYNAMODB_LOCAL_ENDPOINT="http://localhost:8000"
docker compose -f deploy/docker-compose.yaml up -d
./scripts/create-table.sh
```

run the tests
```
go test -v ./...
```

2. Build and push the image to registry

login to registry

```
export TOKEN=<github-token>
echo $TOKEN | docker login ghcr.io -u <user> --password-stdin
```

build the image
```
docker build -t ghcr.io/samirmarin/user-management-service-cicd:<tag> .
```

push the image
```
docker push ghcr.io/samirmarin/user-management-service-cicd:<tag>
```

3. Update deploy/docker-compose.yaml with latest image

Change the image tag for the user-management-service to use the latest build

you can validate the image update process with 
```
docker compose -f deploy/docker-compose.yaml pull user-management-service
docker compose -f deploy/docker-compose.yaml up -d
```