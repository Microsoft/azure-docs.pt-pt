---
title: Guia de publicação de ofertas de contentores no Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de ofertas de contentores no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: c52fabcfc2ff22df2de6dd93f2543d625310baef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484346"
---
# <a name="publishing-guide-for-container-offers"></a>Guia de publicação de ofertas de contentores

As ofertas de contentores ajudam-no a publicar a sua imagem de contentor no Azure Marketplace. Utilize este guia para compreender os requisitos desta oferta. 

As ofertas de contentores são ofertas de transações que são implementadas e faturadas através do Azure Marketplace. A opção de listagem que um utilizador vê é "Get It Now".

Utilize o tipo de oferta *de recipiente* quando a sua solução é uma imagem de contentor Docker que é configurada como uma instância de serviço de contentores Azure baseada em Kubernetes. 

> [!NOTE]
> Exemplos de casos de serviço de contentores Azure baseados em Kubernetes são o Serviço Azure Kubernetes ou instâncias de contentores Azure, a escolha dos clientes Azure para um tempo de funcionamento do contentor baseado em Kubernetes.  

A Microsoft suporta atualmente modelos de licenciamento gratuitos e de trazer a sua própria licença (BYOL).

## <a name="container-offer-requirements"></a>Requisitos de oferta de contentores

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Apoie o modelo de faturação gratuito ou BYOL.<br><br> |  
| Imagem construída a partir de um Dockerfile | As imagens do contentor devem basear-se na especificação de imagem do Docker e construídas a partir de um Dockerfile.<br> <br>Para obter mais informações sobre a construção de imagens Docker, consulte a secção "Utilização" da [referência Dockerfile](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hospedagem num repositório de registo de contentores Azure | As imagens do contentor devem ser acolhidas num repositório de registo de contentores Azure.<br> <br>Para obter mais informações sobre o trabalho com o Registo do Contentor Azure, consulte [Quickstart: Criar um registo privado de contentores utilizando o portal Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Identificação de imagens | As imagens do recipiente devem conter pelo menos uma etiqueta (número máximo de etiquetas: 16).<br><br>Para obter mais informações sobre a marcação de uma imagem, consulte a `docker tag` página no site da [Documentação do Docker.](https://docs.docker.com/engine/reference/commandline/tag)<br><br> |  

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a [fazer crescer o seu negócio na nuvem com o Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [a criação de uma oferta de recipiente Azure](./partner-center-portal/create-azure-container-offer.md) para obter mais informações.
