---
title: Formatos de conteúdo do registro de contêiner do Azure
description: Saiba mais sobre os formatos de conteúdo com suporte no registro de contêiner do Azure, incluindo imagens de contêiner compatíveis com Docker, gráficos Helm, imagens de OCI e artefatos de OCI.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: danlep
ms.openlocfilehash: 38639f22457d923643e8de09cfbbb2fd7f4d2985
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007484"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formatos de conteúdo com suporte no registro de contêiner do Azure

Use um repositório privado no registro de contêiner do Azure para gerenciar um dos seguintes formatos de conteúdo. 

## <a name="docker-compatible-container-images"></a>Imagens de contêiner compatíveis com o Docker

Há suporte para os seguintes formatos de imagem de contêiner do Docker:

* [Manifesto de imagem do Docker v2, esquema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto de imagem do Docker v2, esquema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -inclui listas de manifesto que permitem que os registros armazenem imagens multiplataforma em uma única referência de "imagem: marca"

## <a name="oci-images"></a>Imagens de OCI

O registro de contêiner do Azure dá suporte a imagens que atendem à [especificação de formato de imagem de OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Os formatos de empacotamento incluem o [formato de imagem de singularidade (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefatos de OCI

O registro de contêiner do Azure dá suporte à [especificação de distribuição de OCI](https://github.com/opencontainers/distribution-spec), uma especificação independente de fornecedor e de nuvem para armazenar, compartilhar, proteger e implantar imagens de contêiner e outros tipos de conteúdo (artefatos). A especificação permite que um registro armazene uma grande variedade de artefatos, além de imagens de contêiner. Você usa ferramentas apropriadas para o artefato para enviar e extrair artefatos. Para obter um exemplo, consulte [enviar por push e efetuar pull de um artefato de OCI usando um registro de contêiner do Azure](container-registry-oci-artifacts.md).

Para saber mais sobre os artefatos de OCI, consulte o repositório [do OCI (registro de oras)](https://github.com/deislabs/oras) e o repositório de [artefatos de OCI](https://github.com/opencontainers/artifacts) no github.

## <a name="helm-charts"></a>Gráficos do Helm

O registro de contêiner do Azure pode hospedar repositórios para [gráficos Helm](https://helm.sh/), um formato de empacotamento usado para gerenciar e implantar aplicativos para kubernetes rapidamente. Há suporte para o [cliente Helm](https://docs.helm.sh/using_helm/#installing-helm) versão 2 (2.11.0 ou posterior).

## <a name="next-steps"></a>Passos seguintes

* Consulte como [enviar por push e](container-registry-get-started-docker-cli.md) efetuar pull de imagens com o registro de contêiner do Azure.

* Use [tarefas ACR](container-registry-tasks-overview.md) para compilar e testar imagens de contêiner. 

* Use o [Moby BuildKit](https://github.com/moby/buildkit) para compilar e empacotar contêineres no formato OCI.

* Configure um [repositório Helm](container-registry-helm-repos.md) hospedado no registro de contêiner do Azure. 


