# Criando um Pipeline de Deploy de uma Aplicação Utilizando Gitlab, Docker e Kubernetes

Este projeto demonstra como configurar um pipeline de CI/CD automatizado para uma aplicação Node.js utilizando **GitLab CI/CD**, **Docker** e **Kubernetes**. O objetivo é realizar o deploy da aplicação em dois cenários distintos: em um container Docker e em um cluster Kubernetes hospedado no **Google Cloud Platform (GCP)**.

## 🚀 Objetivos

- Criar uma aplicação Node.js pronta para deploy.
- Configurar pipelines de CI/CD no GitLab.
- Realizar deploy da aplicação em:
  - Container Docker
  - Cluster Kubernetes no GCP

## 📁 Estrutura do Projeto

O projeto está dividido em duas branches principais:

- `feature/docker-deploy`: contém os arquivos para deploy em Docker.
- `feature/kubernetes-deploy`: contém os arquivos para deploy em Kubernetes.

## 🐳 Deploy com Docker

Na branch `feature/docker-deploy`, o arquivo `Dockerfile` define a imagem da aplicação:

```Dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

O pipeline no GitLab é definido no `.gitlab-ci.yml`:

```yaml
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - docker build -t my-nodejs-app .
    - docker push my-nodejs-app

deploy:
  stage: deploy
  script:
    - docker run -d -p 3000:3000 my-nodejs-app
```

## ☸️ Deploy com Kubernetes

Na branch `feature/kubernetes-deploy`, o arquivo `deployment.yaml` configura o deploy no cluster Kubernetes:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodejs
  template:
    metadata:
      labels:
        app: nodejs
    spec:
      containers:
      - name: nodejs
        image: gcr.io/[PROJECT_ID]/nodejs-app:latest
        ports:
        - containerPort: 3000
```

O pipeline no GitLab para Kubernetes é definido no `.gitlab-ci.yml`:

```yaml
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - docker build -t gcr.io/[PROJECT_ID]/nodejs-app:latest .
    - gcloud auth configure-docker
    - docker push gcr.io/[PROJECT_ID]/nodejs-app:latest

deploy:
  stage: deploy
  script:
    - gcloud container clusters get-credentials [CLUSTER_NAME] --zone [ZONE] --project [PROJECT_ID]
    - kubectl apply -f deployment.yaml
```

> ⚠️ Substitua `[PROJECT_ID]`, `[CLUSTER_NAME]` e `[ZONE]` pelos valores reais do seu projeto no GCP.

## ✅ Resultado Esperado

Ao final da configuração, você terá um pipeline de CI/CD funcional que realiza o deploy automático da aplicação Node.js tanto em Docker quanto em Kubernetes.

## 📄 Licença

Este projeto é apenas para fins educacionais.
