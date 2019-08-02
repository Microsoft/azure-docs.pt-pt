---
title: Tutorial – preparar a imagem de contêiner para instâncias de contêiner do Azure
description: Tutorial de instâncias de contêiner do Azure, parte 1 de 3-preparar um aplicativo em uma imagem de contêiner para implantação em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 719237f63d387cf56ab7947f8f168e0aa4351376
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325574"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Tutorial: Criar uma imagem de contêiner para implantação em instâncias de contêiner do Azure

O Azure Container Instances permite a implementação de contentores do Docker na infraestrutura do Azure sem que seja necessário aprovisionar máquinas virtuais ou adotar serviços de nível superior. Neste tutorial, vai compactar uma aplicação Web pequena em Node.js e numa imagem de contentor que pode ser executada com o Azure Container Instances.

Neste artigo, a parte um da série, vai:

> [!div class="checklist"]
> * Clonar o código-fonte da aplicação do GitHub
> * Criar uma imagem de contentor a partir da origem de aplicação
> * Testar a imagem num ambiente local do Docker

Nas partes dois e três do tutorial, vai carregar a imagem para o Azure Container Registry e, em seguida, vai implementá-la no Azure Container Instances.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Obter o código da aplicação

O aplicativo de exemplo neste tutorial é um aplicativo Web simples criado no [node. js][nodejs]. A aplicação serve uma página HTML estática e é semelhante à captura de ecrã seguinte:

![Tutorial de aplicação mostrada no browser][aci-tutorial-app]

Utilizar o Git para clonar o repositório do exemplo de aplicação:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

Você também pode [baixar o arquivo zip][aci-helloworld-zip] do GitHub diretamente.

## <a name="build-the-container-image"></a>Criar a imagem do contentor

O Dockerfile no exemplo de aplicação mostra como é que o contentor é criado. Ele inicia de uma [imagem oficial do node. js][docker-hub-nodeimage] com base no [Alpine Linux][alpine-linux], uma distribuição pequena que é adequada para uso com contêineres. Em seguida, copia os ficheiros da aplicação para o contentor, instala as dependências com o Node Package Manager e, por fim, inicia a aplicação.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Use o comando [Docker Build][docker-build] para criar a imagem de contêiner e marcá-la como *ACI-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

A saída do comando de [Build][docker-build] do Docker é semelhante ao seguinte (truncado para facilitar a leitura):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Use o [][docker-images] comando Docker images para ver a imagem interna:

```bash
docker images
```

A imagem que acabou de criar deve aparecer na lista:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Antes de implantar o contêiner nas instâncias de contêiner do Azure, use o [Docker Run][docker-run] para executá-lo localmente e confirme se ele funciona. O comutador `-d` permite que o contentor seja executado em segundo plano, ao passo que, com `-p`, pode mapear uma porta arbitrária do computador para a porta 80 do contentor.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

O resultado do comando `docker run` apresenta o ID do contentor em execução, caso o comando tenha sido executado com êxito:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Agora, navegue até `http://localhost:8080` no browser para confirmar que o contentor está em execução. Deverá ver uma página Web semelhante à seguinte:

![Executar a aplicação localmente no browser][aci-tutorial-app-local]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma imagem de contentor que pode ser implementada no Azure Container Instances e verificou que é executada localmente. Até agora, fez o seguinte:

> [!div class="checklist"]
> * Clonou a origem da aplicação a partir do GitHub
> * Criou uma imagem de contentor a partir da origem da aplicação
> * Testou o contentor localmente

Avance para o próximo tutorial da série para aprender a armazenar a imagem de contentor no Azure Container Registry:

> [!div class="nextstepaction"]
> [Enviar imagens para o Azure Container Registry](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
