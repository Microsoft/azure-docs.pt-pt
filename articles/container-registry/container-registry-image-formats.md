---
title: Formatos de conteúdo suportados
description: Conheça os formatos de conteúdo suportados pelo Registo de Contentores Azure, incluindo imagens de contentores compatíveis com Docker, gráficos helm, imagens OCI e artefactos OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: b2a54c65d149a27ed9eae85c3308d657ed3471a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008337"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formatos de conteúdo suportados no Registo de Contentores Azure

Utilize um repositório privado no Registo de Contentores Azure para gerir um dos seguintes formatos de conteúdo. 

## <a name="docker-compatible-container-images"></a>Imagens de contentores compatíveis com estivadores

Os seguintes formatos de imagem do contentor Docker são suportados:

* [Docker Image Manifesto V2, Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifesto V2, Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclui Listas Manifestas que permitem que os registos armazenem [imagens multi-arquitetura](push-multi-architecture-images.md) sob uma única `image:tag` referência

## <a name="oci-images"></a>Imagens do OCI

O Registo de Contentores Azure suporta imagens que vão ao encontro da Especificação do [Formato de Imagem da Iniciativa de Contentor Aberto (OCI),](https://github.com/opencontainers/image-spec/blob/master/spec.md)incluindo a especificação opcional do índice de [imagem.](https://github.com/opencontainers/image-spec/blob/master/image-index.md) Os formatos de embalagem incluem [o Formato de Imagem singularidade (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefactos OCI

O Registo de Contentores Azure suporta a [Especificação de Distribuição do OCI,](https://github.com/opencontainers/distribution-spec)uma especificação neutra em nuvem e agnóstica para armazenar, partilhar, proteger e implantar imagens de contentores e outros tipos de conteúdo (artefactos). A especificação permite que um registo armazene uma vasta gama de artefactos para além das imagens de contentores. Usa ferramentas adequadas ao artefacto para empurrar e puxar artefactos. Por exemplo, consulte [Push e puxe um artefacto OCI utilizando um registo de contentores Azure](container-registry-oci-artifacts.md).

Para saber mais sobre os artefactos do OCI, consulte o Registo de [OCI como repo de armazenamento (ORAS)](https://github.com/deislabs/oras) e os [artefactos OCI](https://github.com/opencontainers/artifacts) repo no GitHub.

## <a name="helm-charts"></a>Gráficos Helm

O Registo de Contentores Azure pode acolher repositórios para [gráficos Helm,](https://helm.sh/)um formato de embalagem usado para gerir e implementar rapidamente aplicações para Kubernetes. [A](https://docs.helm.sh/using_helm/#installing-helm) versão do cliente helm 2 (2.11.0 ou mais tarde) é suportada.

## <a name="next-steps"></a>Passos seguintes

* Veja como [empurrar e puxar](container-registry-get-started-docker-cli.md) imagens com o Registo do Contentor Azure.

* Utilize [tarefas ACR](container-registry-tasks-overview.md) para construir e testar imagens de recipientes. 

* Utilize o [Moby BuildKit](https://github.com/moby/buildkit) para construir e embalar recipientes em formato OCI.

* Crie um [repositório helm](container-registry-helm-repos.md) hospedado no Registo de Contentores de Azure. 


