---
title: Tutorial - Use Docker Compose para implantar grupo multi-contentores
description: Use o Docker Compose para construir e executar uma aplicação multi-contentor e, em seguida, apresentar a aplicação em instâncias de contentores Azure
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913845"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Tutorial: Implementar um grupo multi-contentores usando Docker Compose 

Neste tutorial, você usa [Docker Compose](https://docs.docker.com/compose/) para definir e executar uma aplicação de vários contentores localmente e, em seguida, implantá-lo como um [grupo de contentores](container-instances-container-groups.md) em Instâncias de Contentores Azure. 

Executar recipientes em Azure Container Instances on demand quando desenvolver aplicações nativas de nuvem com Docker e você quer mudar perfeitamente do desenvolvimento local para a implementação em nuvem. Esta capacidade é possibilitada pela [integração entre Docker e Azure.](https://docs.docker.com/engine/context/aci-integration/) Pode utilizar comandos nativos do Docker para executar [uma única instância](quickstart-docker-cli.md) de contentor ou um grupo multi-contentor em Azure.

> [!IMPORTANT]
> Nem todas as características de Azure Container Instances são suportadas. Fornecer feedback sobre a integração Docker-Azure criando um problema no repositório de integração do [Docker ACI](https://github.com/docker/aci-integration-beta) GitHub.

> [!TIP]
> Você pode usar a [extensão Docker para Visual Studio Code](https://aka.ms/VSCodeDocker) para uma experiência integrada para desenvolver, executar e gerir recipientes, imagens e contextos.

Neste artigo, irá:

> [!div class="checklist"]
> * Criar um registo de contentor do Azure
> * Clonar o código-fonte da aplicação do GitHub
> * Use Docker Compose para construir uma imagem e executar uma aplicação de vários contentores localmente
> * Empurre a imagem da aplicação para o registo do seu contentor
> * Crie um contexto azul para Docker
> * Apresentar a aplicação em Instâncias de Contentores Azure

## <a name="prerequisites"></a>Pré-requisitos

* **Azure CLI** - Deve ter o CLI Azure instalado no seu computador local. Recomenda-se a versão 2.10.1 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* **Docker Desktop** - Tem de utilizar a versão 2.3.0.5 ou mais tarde do Docker Desktop, disponível para [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) ou [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Ou instale o [CLI de Integração do Docker ACI para o Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Obter o código da aplicação

O exemplo de aplicação neste tutorial é uma aplicação de votos básica. A aplicação consiste num componente Web front-end e uma instância do Redis em back-end. O componente Web é compactado numa imagem personalizada de contentor. A instância do Redis utiliza uma imagem inalterada do Hub do Docker.

Utilize o [git](https://git-scm.com/downloads) para clonar o exemplo de aplicação para o seu ambiente de desenvolvimento:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Mude para o diretório clonado.

```console
cd azure-voting-app-redis
```

Dentro do diretório está o código fonte de aplicação e um ficheiro de composição pré-criado do Docker, docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Modificar o ficheiro de composição do Docker

Abra o docker-compose.yaml num editor de texto. O ficheiro configura os `azure-vote-back` `azure-vote-front` serviços e os serviços.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

Na `azure-vote-front` configuração, faça as duas alterações seguintes:

1. Atualize a `image` propriedade no `azure-vote-front` serviço. Prefixar o nome da imagem com o nome do servidor de login do seu registo de contentores Azure, \<acrName\> .azurecr.io. Por exemplo, se o seu registo for nomeado *miogiça,* o nome do servidor de login é *myregistry.azurecr.io* (todos minúsculos), e a propriedade de imagem é então `myregistry.azurecr.io/azure-vote-front` .
1. Altere o `ports` mapeamento para `80:80` . Guarde o ficheiro.

O ficheiro atualizado deve ser semelhante ao seguinte:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Ao fazer estas substituições, a `azure-vote-front` imagem que constrói no passo seguinte está marcada para o seu registo de contentores Azure, e a imagem pode ser puxada para funcionar em Instâncias de Contentores Azure.

> [!TIP]
> Não é preciso usar um registo de contentores Azure para este cenário. Por exemplo, pode escolher um repositório privado no Docker Hub para hospedar a sua imagem de aplicação. Se escolher um registo diferente, atualize a propriedade da imagem adequadamente.

## <a name="run-multi-container-application-locally"></a>Executar aplicação de vários contentores localmente

Executar [o docker-compose,](https://docs.docker.com/compose/reference/up/)que usa o ficheiro de amostra `docker-compose.yaml` para construir a imagem do recipiente, baixar a imagem redis e iniciar a aplicação:

```console
docker-compose up --build -d
```

Ao concluir, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas. Foram transferidas ou criadas três imagens. A `azure-vote-front` imagem contém a aplicação frontal, que usa a imagem como `uwsgi-nginx-flask` base. A imagem `redis` é utilizada para iniciar uma instância do Redis.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Executar o [comando do PS](https://docs.docker.com/engine/reference/commandline/ps/) para ver os contentores de funcionamento:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Para ver a aplicação em execução, introduza `http://localhost:80` num navegador Web local. O exemplo de aplicação é carregado, conforme mostrado no exemplo seguinte:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Imagem de aplicação de voto":::

Depois de experimentar a aplicação local, passe [o estivador para baixo](https://docs.docker.com/compose/reference/down/) para parar a aplicação e remover os recipientes.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Empurre a imagem para o registo do contentor

Para implementar a aplicação em Instâncias de Contentores Azure, é necessário empurrar a imagem para o registo do `azure-vote-front` seu contentor. Executar [o empurrão de estiva-composição](https://docs.docker.com/compose/reference/push) para empurrar a imagem:

```console
docker-compose push
```

Pode levar alguns minutos para empurrar para o registo.

Para verificar se a imagem está armazenada no seu registo, verifique o comando [do repositório az acr:](/cli/azure/acr/repository#az-acr-repository-show)

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Implementar aplicação para instâncias do contentor Azure

Em seguida, mude para o contexto da ACI. Os comandos subsequentes do Docker são executados neste contexto.

```console
docker context use myacicontext
```

Corra `docker compose up` para iniciar a aplicação em Instâncias de Contentores Azure. A `azure-vote-front` imagem é retirada do registo do seu contentor e o grupo de contentores é criado em Instâncias de Contentores Azure.

```console
docker compose up
```

> [!NOTE]
> Os comandos Docker Compose atualmente disponíveis num contexto ACI são `docker compose up` e `docker compose down` . Não há hífen entre `docker` e `compose` nestes comandos.

Em pouco tempo, o grupo de contentores é implantado. Resultado do exemplo:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Corra `docker ps` para ver os recipientes de funcionamento e o endereço IP atribuído ao grupo de contentores.

```console
docker ps
```

Resultado do exemplo:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Para ver a aplicação de execução na nuvem, insira o endereço IP apresentado num navegador web local. Neste exemplo, `52.179.23.131` insira. O exemplo de aplicação é carregado, conforme mostrado no exemplo seguinte:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Imagem de aplicação de voto no ACI":::

Para ver os troncos do recipiente frontal, executar o comando de registos de [estivadores.](https://docs.docker.com/engine/reference/commandline/logs) Por exemplo:

```console
docker logs azurevotingappredis_azure-vote-front
```

Também pode utilizar o portal Azure ou outras ferramentas Azure para ver as propriedades e o estado do grupo de contentores que implementou.

Quando terminar de experimentar a aplicação, pare a aplicação e os recipientes `docker compose down` com:

```console
docker compose down
```

Este comando elimina o grupo de contentores em Instâncias de Contentores Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você usou Docker Compose para mudar de executar uma aplicação de vários contentores localmente para executar em Instâncias de Contentores Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar um registo de contentor do Azure
> * Clonar o código-fonte da aplicação do GitHub
> * Use Docker Compose para construir uma imagem e executar uma aplicação de vários contentores localmente
> * Empurre a imagem da aplicação para o registo do seu contentor
> * Crie um contexto azul para Docker
> * Apresentar a aplicação em Instâncias de Contentores Azure

Também pode utilizar a [extensão Docker para o Código do Estúdio Visual](https://aka.ms/VSCodeDocker) para uma experiência integrada para desenvolver, executar e gerir contentores, imagens e contextos.

Se quiser tirar partido de mais funcionalidades em Instâncias de Contentores Azure, utilize ferramentas Azure para especificar um grupo multi-contentores. Por exemplo, consulte os tutoriais para implantar um grupo de contentores utilizando o CLI Azure com um [ficheiro YAML,](container-instances-multi-container-yaml.md)ou implementar utilizando um [modelo de Gestor de Recursos Azure](container-instances-multi-container-group.md). 