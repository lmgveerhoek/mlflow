# MLflow Server Setup

This repository contains the necessary files to set up an MLflow server using Docker Compose, with PostgreSQL for backend storage and MinIO for artifact storage.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Quick Start

1. Clone this repository:
   ```
   git clone <repository-url>
   cd <repository-directory>
   ```

2. Copy the example environment file and edit it with your desired settings:
   ```
   cp .env.example .env
   ```
   Open `.env` in a text editor and modify the values as needed.

3. Build and start the services:
   ```
   docker-compose up -d
   ```

4. Access the MLflow UI by navigating to `http://localhost:5000` in your web browser (or the domain you specified in the `.env` file).

## Environment Variables

The `.env` file contains important configuration settings. Make sure to set these correctly:

- `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`: PostgreSQL database settings
- `MINIO_ROOT_USER`, `MINIO_ROOT_PASSWORD`: MinIO access credentials
- `MINIO_BUCKET_NAME`: The S3 bucket name for artifact storage
- `MLFLOW_DOMAIN`: The domain name for accessing the MLflow server

## Managing the Services

To start the services:
```
docker-compose up -d
```

To stop the services:
```
docker-compose down
```

To view logs:
```
docker-compose logs
```

To restart a specific service (e.g., mlflow):
```
docker-compose restart mlflow
```

## Data Persistence

Data is persisted in Docker volumes:
- PostgreSQL data: `./postgres-data`
- MinIO data: `./minio-data`

## Data Migration

When you need to migrate the data stored in the PostgreSQL database and MinIO bucket, follow these steps:

1. For PostgreSQL:
   - Use `pg_dump` to create a backup of your database:
     ```
     docker exec postgres-mlflow pg_dump -U <POSTGRES_USER> <POSTGRES_DB> > mlflow_db_backup.sql
     ```
   - To restore on a new instance:
     ```
     cat mlflow_db_backup.sql | docker exec -i postgres-mlflow psql -U <POSTGRES_USER> <POSTGRES_DB>
     ```

2. For MinIO:
   - Use the MinIO Client (`mc`) to sync data:
     ```
     docker run --rm -v ./minio-data:/data minio/mc mirror /data minio/<NEW_MINIO_INSTANCE>/<BUCKET_NAME>
     ```

Remember to adjust hostnames, credentials, and paths according to your setup when performing these operations.

## Accessing Services

- MLflow UI: `http://<MLFLOW_DOMAIN>:5000`
- MinIO Console: `http://localhost:9001`
- PostgreSQL: `localhost:5432` (accessible from host machine)

## Troubleshooting

If you encounter any issues:
1. Check the logs: `docker-compose logs`
2. Ensure all environment variables in the `.env` file are set correctly
3. Verify that the required ports are not in use by other services
4. Check if all containers are running: `docker-compose ps`

## Updating

To update the services to the latest versions:

1. Pull the latest changes from the repository
2. Update the images: `docker-compose pull`
3. Rebuild and restart the services: `docker-compose up -d --build`

## Security Considerations

- Change default passwords in the `.env` file
- Consider using Docker secrets for sensitive information in a production environment
- Ensure proper network security measures are in place, especially if exposing services to the internet

For more detailed information, refer to the [MLflow documentation](https://www.mlflow.org/docs/latest/index.html).
