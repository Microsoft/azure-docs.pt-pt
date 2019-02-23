---
title: Os contentores oferecem a publicação de guia para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar os contentores no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 97a033e9e539bddb600a450d358b81e3d31c1e1e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731366"
---
# <a name="containers-offer-publishing-guide"></a>Os contentores oferecem o guia de publicação

Ofertas de contentor ajudarão a publicar a sua imagem de contentor no Azure Marketplace. Utilize este guia para compreender os requisitos para esta oferta. 

Estes são ofertas de transação que são implementadas e faturadas através do Marketplace. A chamada a ação que um utilizador vê é a "Obter agora."

Utilize o tipo de oferta de contentor quando a sua solução é uma imagem de contentor do Docker aprovisionada como um serviço de contentor do Azure com base no Kubernetes.

>[!NOTE]
>Por exemplo, um serviço de contentor do Azure com base em Kubernetes como o serviço Kubernetes do Azure ou Azure Container Instances, a escolha de clientes do Azure para um runtime de contentor com base no Kubernetes.  

Atualmente, a Microsoft suporta modelos de licenciamento de gratuitos e bring-your-own-license (BYOL).

## <a name="containers-offer"></a>Oferta de contentores

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Suporta qualquer um dos gratuito ou modelo de faturação de BYOL. |  
| Imagem criada a partir do Dockerfile | Imagens de contentor têm de ser baseadas na especificação de imagem do Docker e devem ser criadas a partir de um Dockerfile.<ul> <li>Para obter mais informações sobre a criação de imagens do docker, visite a seção de utilização localizada em [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hospedagem no ACR | Imagens de contentor tem de estar alojadas num repositório do Azure Container Registry (ACR).<ul> <li>Para obter mais informações sobre como trabalhar com o ACR, visite o guia de introdução: Criar um registo de contentor com a página do portal do Azure localizada em [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Marcação de imagem | Imagens de contentor tem de conter, pelo menos, 1 etiqueta (máximo de etiquetas: 16).<ul> <li>Para mais informações sobre a marcação de uma imagem, visite a página de etiqueta do docker localizada em [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  


## <a name="next-steps"></a>Passos Seguintes

Se ainda não fez isso, 

- [Registar](https://azuremarketplace.microsoft.com/sell) no marketplace.

Se é registrado e estiver a criar uma nova oferta ou trabalhando num já existente

- [Inicie sessão no Portal de parceiro de Cloud](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
- Ver [contentores](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para obter mais informações.
