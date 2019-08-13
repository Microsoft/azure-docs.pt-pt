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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67184815"
---
## <a name="push-image-to-registry"></a>Enviar imagem para o registo

Para enviar uma imagem para um registo do Azure Container, primeiro tem de ter uma imagem. Se você ainda não tiver nenhuma imagem de contêiner local, execute o seguinte comando [Docker pull][docker-pull] para efetuar pull de uma imagem existente do Hub do Docker. Para este exemplo, puxe a `hello-world` imagem.

```
docker pull hello-world
```

Antes de emitir uma imagem para o registo, tem de o etiquetar com o nome completamente qualificado do seu servidor de início de sessão ACR. O nome do servidor de logon está no formato  *\<Registry-\>Name. azurecr.Io* (todas as letras minúsculas), por exemplo, *mycontainerregistry007.azurecr.Io*.

Crie uma etiqueta para a imagem com o comando [etiqueta do docker][docker-tag]. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão da sua instância do ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Por último, utilize o [envio do docker][docker-push] para enviar a imagem para a instância do ACR. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão da sua instância do ACR. Este exemplo cria o repositório **Olá, mundo** , que contém `hello-world:v1` a imagem.

```
docker push <acrLoginServer>/hello-world:v1
```

Depois de enviar a imagem para o registro de contêiner, `hello-world:v1` remova a imagem do ambiente do Docker local. (Observe que esse comando [Docker RMI][docker-rmi] não remove a imagem do repositório **Hello-World** no registro de contêiner do Azure.)

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

