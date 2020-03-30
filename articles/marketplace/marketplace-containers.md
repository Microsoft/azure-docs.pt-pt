---
title: Containers oferecem guia editorial para o Mercado Azure
description: Este artigo descreve os requisitos para publicar contentores no Mercado
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277311"
---
# <a name="containers-offer-publishing-guide"></a>Guia de publicação de oferta de contentores

As ofertas de contentores ajudam-no a publicar a sua imagem de contentor no Mercado Azure. Utilize este guia para compreender os requisitos desta oferta. 

Estas são ofertas de transações que são implementadas e faturadas através do Marketplace. A chamada à ação que um utilizador vê é "Get It Now".

Utilize o tipo de oferta do Recipiente quando a sua solução for uma imagem de contentor Docker aprovisionada como um serviço de contentores Azure baseado em Kubernetes.

>[!NOTE]
>Por exemplo, um serviço de contentores Azure baseado em Kubernetes, como o Azure Kubernetes Service ou o Azure Container Instances, a escolha dos clientes Azure para um tempo de execução de contentores baseado em Kubernetes.  

A Microsoft suporta atualmente modelos de licenciamento gratuitos e de porta-a-licença (BYOL).

## <a name="containers-offer"></a>Oferta de contentores

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Apoie o modelo de faturação gratuito ou BYOL. |  
| Imagem construída a partir de Dockerfile | As imagens do recipiente devem basear-se na especificação de imagem do Docker e devem ser construídas a partir de um Dockerfile.<ul> <li>Para mais informações sobre imagens de estivadores de construção, visite a secção de Utilização localizada em [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hospedagem em ACR | As imagens do contentor devem ser alojadas num repositório de registo de contentores Azure (ACR).<ul> <li>Para mais informações sobre o trabalho com a ACR, visite o Quickstart: Crie um registo de contentores utilizando a página do portal Azure localizada em [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Etiquetagem de imagens | As imagens do recipiente devem conter pelo menos 1 etiqueta (etiquetas máximas: 16).<ul> <li>Para mais informações sobre a marcação de uma imagem, visite a página de marcação de estivar localizada em [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, 

- [Registe-se](https://azuremarketplace.microsoft.com/sell) no mercado.

Se está registado e está a criar uma nova oferta ou a trabalhar numa já existente,

- [Inicie sessão no Cloud Partner Portal](https://cloudpartner.azure.com) para criar ou completar a sua oferta.
- Consulte os [contentores](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para mais informações.
