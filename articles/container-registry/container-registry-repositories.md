---
title: Repositórios de registo de contentor do Azure no portal do Azure
description: Como ver repositórios de registo de contentor do Azure no portal do Azure.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710180"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Ver repositórios de registo de contentor no portal do Azure

O Azure Container Registry permite-lhe armazenar Docker imagens de contentor nos repositórios. Ao armazenar imagens nos repositórios, pode armazenar grupos de imagens (ou versões de imagens) em ambientes isolados. Pode especificar estes repositórios quando enviar imagens para o registo e ver os seus conteúdos no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentor**: Crie um registo de contentor na sua subscrição do Azure. Por exemplo, utilizar o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Instale [Docker] [ docker-install] no seu computador local, que fornece a interface de linha de comandos do Docker.
* **Imagem de contentor**: Envie uma imagem para o registo de contentor. Para obter orientações sobre como enviar e receber imagens, consulte [Push e solicitar uma imagem](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Ver repositórios no portal do Azure

Pode ver uma lista de repositórios do alojamento de suas imagens, bem como as marcas de imagem, no portal do Azure.

Se tiver seguido os passos em [Push e solicitar uma imagem](container-registry-get-started-docker-cli.md) (e, em seguida, não a eliminar a imagem), deve ter uma imagem da Nginx no seu registo de contentor. As instruções no artigo especificado que Etiquetar a imagem com um espaço de nomes, "amostras" no `/samples/nginx`. Como um atualizador, o [push do docker] [ docker-push] foi do comando especificado nesse artigo:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Como o Azure Container Registry suporta espaços de nomes esses repositórios com múltiplos níveis, pode definir o âmbito de coleções de imagens relacionadas com uma aplicação específica ou uma coleção de aplicações, para desenvolvimento diferentes equipas ou operacionais. Para obter mais informações sobre os repositórios de registos de contentores, consulte [registos de contentores privado do Docker no Azure](container-registry-intro.md).

Para ver um repositório:

1. Entrar para a [portal do Azure][portal]
1. Selecione o **Azure Container Registry** ao qual que enviou a imagem da Nginx
1. Selecione **repositórios** para ver uma lista dos repositórios que contêm as imagens no registo
1. Selecione um repositório para ver as etiquetas de imagem dentro desse repositório

Por exemplo, se enviou a imagem da Nginx como instruções na [Push e solicitar uma imagem](container-registry-get-started-docker-cli.md), deverá ver algo semelhante a:

![Repositórios no portal](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe as noções básicas de visualizar e trabalhar com repositórios no portal, experimente utilizar o Azure Container Registry com um [do Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) cluster.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
