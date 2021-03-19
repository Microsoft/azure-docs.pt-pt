---
title: Ver repositórios no portal
description: Utilize o portal Azure para visualizar os repositórios do Registo de Contentores Azure, que acolhem imagens de contentores Docker e outros artefactos suportados.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456286"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Ver repositórios de registo de contentores no portal Azure

O Registo de Contentores Azure permite armazenar imagens de contentores Docker em repositórios. Ao armazenar imagens em repositórios, pode armazenar grupos de imagens (ou versões de imagens) em ambientes isolados. Pode especificar estes repositórios quando empurra imagens para o seu registo e vê o seu conteúdo no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo do contentor**: Crie um registo de contentores na sua assinatura Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Instale [o Docker][docker-install] na sua máquina local, que lhe fornece a interface da linha de comando Docker.
* **Imagem do recipiente**: Empurre uma imagem para o registo do seu contentor. Para obter orientações sobre como empurrar e puxar imagens, consulte [Push e puxe uma imagem](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Ver repositórios no portal Azure

Pode ver uma lista dos repositórios que acolhem as suas imagens, bem como as etiquetas de imagem, no portal Azure.

Se seguiu os passos em [Push e puxou uma imagem](container-registry-get-started-docker-cli.md) (e não eliminou posteriormente a imagem), deverá ter uma imagem Nginx no registo do seu contentor. As instruções desse artigo especificaram que marca a imagem com um espaço com nome, as "amostras" em `/samples/nginx` . Como refrescante, o comando [de empurrar estivado][docker-push] especificado nesse artigo foi:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Como o Registo de Contentores Azure suporta tais espaços de nomes de repositórios multinível, você pode estender as coleções de imagens relacionadas com uma aplicação específica, ou uma coleção de apps, para diferentes equipas de desenvolvimento ou operacionais. Para ler mais sobre repositórios em registos de contentores, consulte [os registos de contentores private Docker em Azure](container-registry-intro.md).

Para ver um repositório:

1. Inicie sessão no [portal do Azure][portal]
1. Selecione o **Registo do Contentor Azure** ao qual empurrou a imagem Nginx
1. **Selecione Repositórios** para ver uma lista dos repositórios que contêm as imagens no registo
1. Selecione um repositório para ver as etiquetas de imagem dentro desse repositório

Por exemplo, se empurrou a imagem Nginx como instruído em [Push e puxar uma imagem,](container-registry-get-started-docker-cli.md)deve ver algo semelhante a:

![Repositórios no portal](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Passos seguintes

Agora que conhece os fundamentos da visualização e do trabalho com repositórios no portal, tente utilizar o Registo de Contentores Azure com um cluster [Azure Kubernetes Service (AKS).](../aks/tutorial-kubernetes-prepare-app.md)

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
