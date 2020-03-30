---
title: (DEPRECIADO) Gerir o cluster Azure Swarm com a API do Docker
description: Desloque contentores para um aglomerado de estivadores de estivano no Serviço de Contentores Azure
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: c76701ce74aafcccdbb2f1a2454f9528b52fc096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136232"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>(DEPRECIADO) Gestão de contentores com Docker Swarm

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O Docker Swarm fornece um ambiente para a implementação de cargas de trabalho de conteúdo através de um conjunto agrupado de anfitriões de Docker. O Docker Swarm utiliza a API do Docker nativa. O fluxo de trabalho para gerir contentores num Docker Swarm é quase idêntico ao que de um anfitrião de contentor único. Este documento fornece exemplos simples de implementação de cargas de trabalho de conteúdo numa instância de Serviço de Contentor do Azure do Docker Swarm. Para obter documentação mais detalhada sobre o Docker Swarm, consulte [Docker Swarm em Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Pré-requisitos para os exercícios deste documento:

[Criar um cluster Swarm no Serviço de Contentor do Azure](container-service-deployment.md)

[Ligar com o cluster Swarm no Serviço de Contentor do Azure](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Implementar um novo contentor
Para criar um novo contentor no Docker Swarm, utilize o comando `docker run` (certificando-se de que tem aberto um túnel SSH para os principais, de acordo com os pré-requisitos acima). Este exemplo cria um contentor a partir da imagem `yeasy/simple-web`:

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Após a criação do contentor, utilize `docker ps` para apresentar informações sobre o contentor. Repare que o agente do Swarm que está a alojar o contentor está listado:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Agora pode aceder à aplicação que está em execução neste contentor através do nome DNS público do load balancer do agente Swarm. Pode encontrar estas informações no Portal do Azure:  

![Resultados da visita reais](./media/container-service-docker-swarm/real-visit.jpg)  

Por predefinição, o Balanceador de Carga tem as portas 80, 8080 e 443 abertas. Se pretender ligar-se a outra porta terá de abrir essa porta no Balanceador de Carga do Azure para o Agrupamento de Agentes.

## <a name="deploy-multiple-containers"></a>Implementar vários contentores
Como os vários contentores são iniciados, executando "docker run" várias vezes, pode utilizar o comando `docker ps` para ver em que os anfitriões os contentores estão em execução. No exemplo abaixo, três contentores são distribuídos uniformemente por três agentes Swarm:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Implementar contentores utilizando o Docker Compose
Pode utilizar o Docker Compose para automatizar a implementação e a configuração de vários contentores. Para tal, certifique-se de que foi criado um túnel Secure Shell (SSH) e que a variável DOCKER_HOST foi definida (ver os pré-requisitos acima).

Crie um ficheiro docker-compose.yml no sistema local. Para isso, utilize esta amostra:

```dockerfile
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Execute `docker-compose up -d` para iniciar as implementações de contentores:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Por fim, a lista de contentores em execução será apresentada. Esta lista reflete os contentores que foram implementados utilizando o Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturalmente, pode utilizar `docker-compose ps` para examinar apenas os contentores definidos noficheiro `compose.yml`.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Docker Swarm](https://docs.docker.com/swarm/)

