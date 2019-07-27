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
ms.openlocfilehash: cd97c61e7493249785293ae331713ba1a98efee3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67184814"
---
## <a name="run-image-from-registry"></a>Executar imagem do registro

Agora, você pode efetuar pull e `hello-world:v1` executar a imagem de contêiner do registro de contêiner usando a [execução][docker-run]do Docker:

```
docker run <acrLoginServer>/hello-world:v1  
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
