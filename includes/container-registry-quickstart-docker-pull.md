---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: d7c813c748d2af60ace71f61ba3efa43a18da00d
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752194"
---
## <a name="run-image-from-registry"></a>Executar imagem a partir do registo

Agora, você pode puxar e executar a `hello-world:v1` imagem do contentor a partir do seu registo de contentores usando [a execução do estivador:][docker-run]

```
docker run <login-server>/hello-world:v1  
```

Exemplo de saída: 

```
Unable to find image 'mycontainerregistry007.azurecr.io/hello-world:v1' locally
v1: Pulling from hello-world
Digest: sha256:662dd8e65ef7ccf13f417962c2f77567d3b132f12c95909de6c85ac3c326a345
Status: Downloaded newer image for mycontainerregistry007.azurecr.io/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.

[...]
```

<!-- LINKS - External -->
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
