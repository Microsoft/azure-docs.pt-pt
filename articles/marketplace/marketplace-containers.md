---
title: Containers oferecem guia editorial para o Mercado Azure
description: Este artigo descreve os requisitos para publicar contentores no Mercado
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 80bcf0d5d37a44cee2aab38161abac619542204a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684328"
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

- [Saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o mercado.

Para se registar no Partner Center, comece a criar uma nova oferta ou a trabalhar numa existente:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte a criação de uma oferta de [contentores Azure](./partner-center-portal/create-azure-container-offer.md) para mais informações.
