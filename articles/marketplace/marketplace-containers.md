---
title: Guia de publicação para ofertas de contentores no Azure Marketplace
description: Este artigo descreve os requisitos para publicar ofertas de contentores no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 99aecee930e5d77302ad54babd927588519e33fd
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160465"
---
# <a name="publishing-guide-for-container-offers"></a>Guia de publicação para ofertas de contentores

As ofertas de contentores ajudam-no a publicar a sua imagem de contentor no Azure Marketplace. Utilize este guia para compreender os requisitos desta oferta. 

As ofertas de contentores são ofertas de transações que são implementadas e faturadas através do Azure Marketplace. A chamada à ação que um utilizador vê é "Get It Now".

Utilize o tipo de oferta *do Recipiente* quando a sua solução for uma imagem de contentor Docker que é configurada como uma instância de serviço de contentores Azure baseada em Kubernetes. 

> [!NOTE]
> Exemplos de casos de serviço de contentores Azure baseados em Kubernetes são o Serviço Azure Kubernetes ou as Instâncias de Contentores Azure, a escolha dos clientes Azure para um tempo de execução de contentores baseado em Kubernetes.  

A Microsoft suporta atualmente modelos de licenciamento gratuitos e de porta-a-licença (BYOL).

## <a name="container-offer-requirements"></a>Requisitos de oferta de contentores

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Apoie o modelo de faturação gratuito ou BYOL.<br><br> |  
| Imagem construída a partir de um Dockerfile | As imagens do recipiente devem basear-se na especificação de imagem do Docker e construídas a partir de um Dockerfile.<br> <br>Para obter mais informações sobre a construção de imagens do Docker, consulte a secção "Utilização" da [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hospedagem num repositório de registo de contentores azure | As imagens do contentor devem ser hospedadas num repositório de registo de contentores azure.<br> <br>Para obter mais informações sobre o trabalho com o Registo de Contentores Azure, consulte [Quickstart: Crie um registo de contentores privados utilizando o portal Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).<br><br> |  
| Etiquetagem de imagens | As imagens do recipiente devem conter pelo menos uma etiqueta (número máximo de etiquetas: 16).<br><br>Para obter mais informações sobre `docker tag` a marcação de uma imagem, consulte a página no site de [Documentação do Docker.](https://docs.docker.com/engine/reference/commandline/tag)<br><br> |  

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a cultivar o seu negócio de cloud com o [Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte a criação de uma oferta de [contentores Azure](./partner-center-portal/create-azure-container-offer.md) para mais informações.
