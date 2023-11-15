## Conditional Deployment

The project employs conditional deployment strategies using GitHub Actions workflows to facilitate targeted and automated deployment based on specific conditions or events within the repository.

Conditional deployment allows for the execution of deployment tasks selectively, triggered by particular events or changes. These workflows are designed to deploy services only when relevant changes occur in the associated directories, ensuring precision in deployment processes and avoiding unnecessary deployments.

The conditional deployment setup ensures that only the necessary services are built, updated, or deployed in response to changes pushed to the respective directories, optimizing the deployment workflow and maintaining a streamlined deployment process.


## Ejemplo de Tiempo de Despliegue

### Despliegue Completo

![Despliegue Completo](ruta/a/imagen_despliegue_completo.png)
*Tiempo de despliegue completo: 7m 18s*

En la imagen anterior se muestra el despliegue completo de todas las aplicaciones y servicios, lo cual ha llevado aproximadamente 7 minutos y 18 segundos.

### Despliegue Selectivo - Solo Web App

![Despliegue Selectivo](ruta/a/imagen_despliegue_selectivo.png)
*Tiempo de despliegue selectivo (solo Web App): 1m 19s*

En la segunda imagen, se ilustra el despliegue selectivo únicamente de la aplicación web. Se observa que este proceso ha tardado tan solo 1 minuto y 19 segundos, evidenciando la significativa mejora en el tiempo de despliegue al realizar actualizaciones puntuales en servicios específicos.


## Optimized Conditional Deployment

The project implements targeted conditional deployment strategies through GitHub Actions workflows, optimizing the deployment process by executing specific workflows based on changes made within the repository.

When changes are made solely in a specific service directory, such as `project-web-app/`, only the corresponding workflow, in this case, the "Build Web App Workflow," is triggered. Consequently, the workflow selectively handles tasks pertinent to that service, such as checking out the updated code, removing and redeploying the container associated with the web application service.

This tailored approach to deployment minimizes unnecessary actions by focusing deployment efforts solely on the affected service. As a result, significant improvements in deployment times are achieved. Reducing the deployment scope to only the affected service avoids the need to rebuild or redeploy unrelated services, contributing to faster and more efficient deployment processes.

By leveraging conditional deployment workflows, this optimized deployment strategy ensures a streamlined and expedited deployment experience, minimizing downtime and enhancing development efficiency.

## Project Structure

The directory structure of this project is organized as follows:

```bash
.
├── .github/
│   └── # Include relevant files for GitHub actions or workflows
├── project-coordination-service/
│   └── # Files and folders related to the coordination service
│   └── Dockerfile
├── project-ingest-service/
│   └── # Files and folders related to the ingest service
│   └── Dockerfile
├── project-web-app/
│   └── # Files and folders related to the web application
│   └── Dockerfile
├── docker-compose.yml
├── README.md
└── # Other relevant files
```

## Docker Compose Structure

The project utilizes Docker Compose for orchestrating and managing the deployment of its services. The `docker-compose.yml` file within the project's root directory defines the configuration for running multiple interconnected containers.

The structure of the Docker Compose file (`docker-compose.yml`) is as follows:

```yml
version: "3.9"

services:

  postgresql:
    image: "postgres:14-alpine"
    container_name: postgresql
    volumes:
      - {volumes}
    ports:
      - {ports}
    environment:
      - {environment}

  app:
    build:
      context: ./project-web-app
    container_name: project-app
    volumes:
      - {volumes}
    ports:
      - {ports}
    environment:
      - {environment}

  project-ingest:
    build: ./project-ingest-service
    container_name: project-ingest
    volumes:
      - {volumes}
    ports:
      - {ports}
    environment:
      - {environment}

  project-coordination:
    build: ./project-coordination-service
    container_name: project-coordination
    volumes:
      - {volumes}
    ports:
      - {ports}
    environment:
      - {environment}
```

## Workflows Structure

The project employs GitHub Actions workflows to automate various tasks and streamline the deployment process for different services within the repository. Each workflow is triggered by specific events, such as pushes to particular directories, and executes a series of defined steps to build, deploy, or manage services accordingly.

The structure of the workflows is organized as follows:

### Build Web App Workflow

This workflow is triggered by pushes to the `project-web-app/` directory. It performs tasks related to the web application, such as checking out the code, cleaning up Docker containers and images, and then building the Docker Compose for the app service.
```yml
name: Build App

on:
  push:
    paths:
      - 'project-web-app/**'

jobs:
  CheckOut:
    name: Check out
    runs-on: [self-hosted, linux, x64]
    steps:
      - uses: actions/checkout@v1

  DockerClean:
    name: Docker clean
    needs: CheckOut
    runs-on: [self-hosted, linux, x64]
    steps:
      - name: Stop project-app Container
        run: sudo docker stop project-app
        continue-on-error: true
      - name: Remove project-app Container
        run: sudo docker rm project-app
        continue-on-error: true
      - name: Remove project-all-app Image
        run: sudo docker rmi project-all-app
        continue-on-error: true

  DockerCompose:
    name: Docker compose
    needs: DockerClean
    runs-on: [self-hosted, linux, x64]
    steps:
      - name: Build the docker-compose
        run: sudo docker compose -f "docker-compose.yml" up -d --build app
```

### Build Ingest Service Workflow

Triggered by pushes to the `project-ingest-service/` directory, this workflow manages tasks associated with the ingest service. It includes steps for checking out the code, cleaning up Docker containers and images specific to the ingest service, and building the Docker Compose for the ingest service.
```yml
name: Build Ingest Service

on:
  push:
    paths:
      - 'project-ingest-service/**'

jobs:
  CheckOut:
    name: Check out
    runs-on: [self-hosted, linux, x64]
    steps:
      - uses: actions/checkout@v1

  DockerClean:
    name: Docker clean
    needs: CheckOut
    runs-on: [self-hosted, linux, x64]
    steps:
      - name: Stop project-ingest Container
        run: sudo docker stop project-ingest
        continue-on-error: true
      - name: Remove project-ingest Container
        run: sudo docker rm project-ingest
        continue-on-error: true
      - name: Remove project-all-project-ingest Image
        run: sudo docker rmi project-all-project-ingest
        continue-on-error: true

  DockerCompose:
    name: Docker compose
    needs: DockerClean
    runs-on: [self-hosted, linux, x64]
    steps:
      - name: Build the docker-compose
        run: sudo docker compose -f "docker-compose.yml" up -d --build project-ingest
```

### Build Coordination Service Workflow

This workflow is initiated by pushes to the `project-coordination-service/` directory. It executes similar steps as the other workflows, including checking out the code, cleaning up Docker containers and images related to the coordination service, and building the Docker Compose for the coordination service.
```yml
name: Build Coordination Service

on:
  push:
    paths:
      - 'project-coordination-service/**'

jobs:
  CheckOut:
    name: Check out
    runs-on: [self-hosted, linux, x64]
    steps:
      - uses: actions/checkout@v1

  DockerClean:
    name: Docker clean
    needs: CheckOut
    runs-on: [self-hosted, linux, x64]
    steps:
      - name: Stop project-coordination Container
        run: sudo docker stop project-coordination
        continue-on-error: true
      - name: Remove project-coordination Container
        run: sudo docker rm project-coordination
        continue-on-error: true
      - name: Remove project-all-project-coordination Image
        run: sudo docker rmi project-all-project-coordination
        continue-on-error: true

  DockerCompose:
    name: Docker compose
    needs: DockerClean
    runs-on: [self-hosted, linux, x64]
    steps:
      - name: Build the docker-compose
        run: sudo docker compose -f "docker-compose.yml" up -d --build project-coordination
```

These workflows enable automated deployment and management of the various services within the project, ensuring efficient and consistent execution of tasks based on changes pushed to specific directories.


