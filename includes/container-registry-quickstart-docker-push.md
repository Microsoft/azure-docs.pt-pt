---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184815"
---
## <a name="push-image-to-registry"></a>Enviar imagem para o registo

Para enviar uma imagem para um registo do Azure Container, primeiro tem de ter uma imagem. Se ainda não tiver imagens de contentores locais, execute o seguinte comando de puxar o [estivador][docker-pull] para puxar uma imagem existente do Docker Hub. Para este exemplo, `hello-world` puxe a imagem.

```
docker pull hello-world
```

Antes de emitir uma imagem para o registo, tem de o etiquetar com o nome completamente qualificado do seu servidor de início de sessão ACR. O nome do servidor de login está no * \<nome\>do registo* de formato .azurecr.io (toda a minúscula), por exemplo, *mycontainerregistry007.azurecr.io*.

Crie uma etiqueta para a imagem com o comando [etiqueta do docker][docker-tag]. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão da sua instância do ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Por último, utilize o [envio do docker][docker-push] para enviar a imagem para a instância do ACR. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão da sua instância do ACR. Este exemplo cria o repositório do `hello-world:v1` mundo **olá,** contendo a imagem.

```
docker push <acrLoginServer>/hello-world:v1
```

Depois de empurrar a imagem para o `hello-world:v1` registo do seu contentor, retire a imagem do ambiente local do Docker. (Note que este comando [rmi estivador][docker-rmi] não remove a imagem do repositório **do mundo olá** no registo do seu contentor Azure.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

