---
title: (PRETERIDO) Tutorial do Azure Container Service - preparar aplicação
description: Tutorial do Azure Container Service - Preparar Aplicação
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 961f09d7581a26596b03e7a2f8e97d98fe83e6aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995369"
---
# <a name="deprecated-create-container-images-to-be-used-with-azure-container-service"></a>(PRETERIDO) Criar imagens de contentor a ser utilizado com o Azure Container Service

> [!TIP]
> Para a versão atualizada neste tutorial que utiliza o Azure Kubernetes Service, consulte [Tutorial: Preparar um aplicativo para o Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-prepare-app.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste tutorial, a parte um de sete, uma aplicação de contentores múltiplos é preparada para utilização com o Kubernetes. Os passos concluídos incluem:  

> [!div class="checklist"]
> * A clonagem de origens das aplicações a partir do GitHub  
> * Criar uma imagem de contentor a partir da origem de aplicação
> * Testar a aplicação num ambiente local do Docker

Depois de concluída, a aplicação seguinte fica acessível no seu ambiente de desenvolvimento local.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Nos tutoriais subsequentes, a imagem do contentor é carregada para o Azure Container Registry e, em seguida, é executada num cluster do Kubernetes alojado no Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial pressupõe conhecimentos básicos dos principais conceitos do Docker, como contentores, imagens de contentores e comandos simples do Docker. Se for necessário, veja [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para obter um manual sobre as noções básicas dos contentores. 

Para concluir este tutorial, precisa de um ambiente de desenvolvimento do Docker. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

O Azure Cloud Shell não inclui os componentes do Docker necessários para concluir todos os passos deste tutorial. Por conseguinte, recomendamos a utilização de um ambiente de desenvolvimento completo do Docker.

## <a name="get-application-code"></a>Obter o código da aplicação

O exemplo de aplicação neste tutorial é uma aplicação de votos básica. A aplicação consiste num componente Web front-end e uma instância do Redis em back-end. O componente Web é compactado numa imagem personalizada de contentor. A instância do Redis utiliza uma imagem inalterada do Hub do Docker.  

Utilize o git para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Altere os diretórios para trabalhar a partir do diretório clonado.

```
cd azure-voting-app-redis
```

Dentro do diretório estão: o código de origem da aplicação, um ficheiro de composição do Docker e um ficheiro de manifesto do Kubernetes. Estes ficheiros são utilizados em todo o tutorial. 

## <a name="create-container-images"></a>Criar imagens de contentor

[O Docker Compose](https://docs.docker.com/compose/) pode ser utilizado para automatizar a compilação de imagens de contentor e a implementação de aplicações com vários contentores.

Execute o ficheiro `docker-compose.yml` para criar a imagem de contentor, transferir a imagem de Redis e iniciar a aplicação.

```bash
docker-compose up -d
```

Ao concluir, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas.

```bash
docker images
```

Repare que foram transferidas ou criadas três imagens. A imagem `azure-vote-front` contém a aplicação e utilizar a imagem `nginx-flask` como base. A imagem `redis` é utilizada para iniciar uma instância do Redis.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Execute o comando [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) para ver os contentores em execução.

```bash
docker ps
```

Saída:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testar a aplicação localmente

Procure `http://localhost:8080` para ver a aplicação em execução.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que a funcionalidade da aplicação foi validada, os contentores em execução podem ser parados e removidos. Não elimine as imagens de contentor. A imagem `azure-vote-front` é carregada para uma instância do Azure Container Registry no tutorial seguinte.

Execute o seguinte para parar os contentores em execução.

```bash
docker-compose stop
```

Elimine os contentores e os recursos parados com o seguinte comando.

```bash
docker-compose down
```

Após a conclusão, tem uma imagem de contentor que contém a aplicação Azure Vote.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, uma aplicação foi testada e imagens de contentor foram criadas para a aplicação. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Clonagem da origem da aplicação a partir do GitHub  
> * Criou uma imagem de contentor a partir da origem de aplicação
> * Testou a aplicação em ambientes do Docker locais

Avance para o próximo tutorial para saber mais sobre o armazenamento das imagens de contentores em registos do Azure Container Registry.

> [!div class="nextstepaction"]
> [Push images to Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md) (Enviar imagens para o Azure Container Registry)
