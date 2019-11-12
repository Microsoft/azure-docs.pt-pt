---
title: Repositórios do registro de contêiner do Azure no portal do Azure
description: Use o portal do Azure para exibir repositórios do registro de contêiner do Azure, que hospedam imagens de contêiner do Docker e outros artefatos com suporte.
services: container-registry
author: cristy
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: jeconnoc
ms.openlocfilehash: 793d8e82ca78c6055a6e956d4f41b7991d5fd700
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931537"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Exibir repositórios de registro de contêiner no portal do Azure

O registro de contêiner do Azure permite armazenar imagens de contêiner do Docker em repositórios. Ao armazenar imagens em repositórios, você pode armazenar grupos de imagens (ou versões de imagens) em ambientes isolados. Você pode especificar esses repositórios ao enviar imagens por push para o registro e exibir seu conteúdo no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Registro de contêiner**: Crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
* **CLI do Docker**: Instale o [Docker][docker-install] em seu computador local, que fornece a interface de linha de comando do Docker.
* **Imagem de contêiner**: enviar por push uma imagem para o registro de contêiner. Para obter orientação sobre como enviar por push e efetuar pull de imagens, consulte [enviar por push e puxar uma imagem](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Exibir repositórios no portal do Azure

Você pode ver uma lista dos repositórios que hospedam suas imagens, bem como as marcas de imagem, na portal do Azure.

Se você seguiu as etapas em [enviar por push e efetuar pull de uma imagem](container-registry-get-started-docker-cli.md) (e não excluiu subsequentemente a imagem), você deve ter uma imagem Nginx no registro de contêiner. As instruções nesse artigo especificaram que você marca a imagem com um namespace, os "exemplos" em `/samples/nginx`. Como um atualizador, o comando [Docker Push][docker-push] especificado no artigo foi:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Como o registro de contêiner do Azure dá suporte a esses namespaces de repositório de vários níveis, você pode delimitar coleções de imagens relacionadas a um aplicativo específico ou a uma coleção de aplicativos para diferentes equipes de desenvolvimento ou operacionais. Para ler mais sobre repositórios em registros de contêiner, consulte [registros de contêiner do Docker privado no Azure](container-registry-intro.md).

Para exibir um repositório:

1. Inicie sessão no [portal do Azure][portal]
1. Selecione o **registro de contêiner do Azure** para o qual você enviou a imagem Nginx
1. Selecione **repositórios** para ver uma lista dos repositórios que contêm as imagens no registro
1. Selecione um repositório para ver as marcas de imagem dentro desse repositório

Por exemplo, se você enviou por push a imagem Nginx conforme instruído em [Enviar e puxar uma imagem](container-registry-get-started-docker-cli.md), verá algo semelhante a:

![Repositórios no portal](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você conhece os conceitos básicos da exibição e do trabalho com repositórios no portal, tente usar o registro de contêiner do Azure com um cluster do [AKs (serviço de kubernetes do Azure)](../aks/tutorial-kubernetes-prepare-app.md) .

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
