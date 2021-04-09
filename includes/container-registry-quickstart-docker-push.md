---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100019092"
---
## <a name="push-image-to-registry"></a>Enviar imagem para o registo

Para enviar uma imagem para um registo do Azure Container, primeiro tem de ter uma imagem. Se ainda não tiver imagens de contentores locais, faça o seguinte [estivador puxar][docker-pull] o comando para puxar uma imagem pública existente. Para este exemplo, retire a `hello-world` imagem do Registo de Contentores da Microsoft.

```
docker pull mcr.microsoft.com/hello-world
```

Antes de poder empurrar uma imagem para o seu registo, tem de a marcar com o nome totalmente qualificado do seu servidor de login de registo. O nome do servidor de login está no formato *\<registry-name\> .azurecr.io* (deve ser todos minúsculos), por exemplo, *mycontainerregistry.azurecr.io*.

Crie uma etiqueta para a imagem com o comando [etiqueta do docker][docker-tag]. Substitua `<login-server>` pelo nome do servidor de início de sessão da sua instância do ACR.

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

Exemplo:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Finalmente, use [o estivador para][docker-push] empurrar a imagem para a instância de registo. `<login-server>`Substitua-o pelo nome do servidor de login da sua instância de registo. Este exemplo cria o repositório **hello-world,** contendo a `hello-world:v1` imagem.

```
docker push <login-server>/hello-world:v1
```

Depois de empurrar a imagem para o registo do seu contentor, retire a `hello-world:v1` imagem do ambiente local do Docker. (Note que este comando [de estivador rmi][docker-rmi] não remove a imagem do repositório **do mundo da hello-world** no seu registo de contentores Azure.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

