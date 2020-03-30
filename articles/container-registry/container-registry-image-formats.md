---
title: Formatos de conteúdo suportado
description: Conheça os formatos de conteúdo suportados pelo Registo de Contentores Azure, incluindo imagens de contentores compatíveis com o Docker, gráficos Helm, imagens OCI e artefactos OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247010"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formatos de conteúdo suportados no Registo de Contentores Do Azure

Utilize um repositório privado no Registo de Contentores Azure para gerir um dos seguintes formatos de conteúdo. 

## <a name="docker-compatible-container-images"></a>Imagens de contentores compatíveis com docker

Os seguintes formatos de imagem de contentor Estivador são suportados:

* [Docker Image Manifest V2, Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2, Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclui Listas de Manifesto que permitem aos registos armazenar imagens multiplataforma sob uma única referência "imagem:tag"

## <a name="oci-images"></a>Imagens oCI

O Registo de Contentores Azure suporta imagens que vão ao encontro da Especificação do Formato de Imagem da [Iniciativa de Contentores Abertos (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md) Os formatos de embalagem incluem [formato de imagem singularidade (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefactos oci

O Registo de Contentores Azure suporta a Especificação de [Distribuição do OCI](https://github.com/opencontainers/distribution-spec), uma especificação neutra em nuvem-agnóstica para armazenar, partilhar, proteger e implantar imagens de contentores e outros tipos de conteúdo (artefactos). A especificação permite que um registo armazene uma vasta gama de artefactos, além de imagens de contentores. Usa ferramentas apropriadas ao artefacto para empurrar e puxar artefactos. Por exemplo, consulte [Empurre e puxe um artefacto OCI utilizando um registo de contentores Azure](container-registry-oci-artifacts.md).

Para saber mais sobre artefactos OCI, consulte o [Registo OCI como repo de armazenamento (ORAS)](https://github.com/deislabs/oras) e o [Repo de Artefactos OCI](https://github.com/opencontainers/artifacts) no GitHub.

## <a name="helm-charts"></a>Gráficos de leme

O Registo de Contentores Azure pode acolher repositórios para [gráficos Helm,](https://helm.sh/)um formato de embalagem usado para gerir e implementar rapidamente aplicações para Kubernetes. [A](https://docs.helm.sh/using_helm/#installing-helm) versão do cliente helm 2 (2.11.0 ou posterior) é suportada.

## <a name="next-steps"></a>Passos seguintes

* Veja como [empurrar e puxar](container-registry-get-started-docker-cli.md) imagens com o Registo de Contentores Azure.

* Utilize [tarefas ACR](container-registry-tasks-overview.md) para construir e testar imagens de contentores. 

* Utilize o [Moby BuildKit](https://github.com/moby/buildkit) para construir e embalar contentores em formato OCI.

* Crie um [repositório Helm](container-registry-helm-repos.md) hospedado no Registo de Contentores Azure. 


