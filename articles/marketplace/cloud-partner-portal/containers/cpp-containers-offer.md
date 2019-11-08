---
title: Oferta de imagem de contêineres do Azure | Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de contêiner no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pabutler
ms.openlocfilehash: f0e71d8135a5dd691dc1f746ec54cfb6170e281d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823238"
---
# <a name="containers"></a>Contentores

<table> <tr> <td>Esta seção explica como publicar uma imagem de contêiner no <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
O tipo de oferta de contêiner oferece suporte a imagens de contêiner do Docker provisionadas como instâncias do <a href="https://docs.microsoft.com/azure/aks/index">serviço kubernetes do Azure</a> ou <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">instâncias de contêiner do Azure</a> e hospedadas em um repositório <a href="https://docs.microsoft.com/azure/container-registry">do registro de contêiner do Azure</a> . </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Componentes da oferta

Esta seção descreve os elementos de publicação de um contêiner e destina-se a ser um guia para o Publicador para o Azure Marketplace. A publicação é dividida nas seguintes partes principais:

- [Pré](./cpp-prerequisites.md) -requisitos – lista os requisitos técnicos e de negócios antes de criar ou publicar uma oferta de contêiner.
- [Criar a oferta](./cpp-create-offer.md) -lista as etapas necessárias para criar uma nova entrada de oferta de contêiner usando o portal do Cloud Partner.
- [Preparar os ativos técnicos](./cpp-create-technical-assets.md) -como criar os ativos técnicos para uma solução de contêiner como uma oferta no Azure Marketplace.
- [Publicar a oferta](./cpp-publish-offer.md) -como enviar a oferta para publicação no Azure Marketplace.

## <a name="container-publishing-process"></a>Processo de publicação de contêiner

O diagrama a seguir ilustra as etapas de alto nível na publicação de uma oferta de VM.
![etapas para publicar uma oferta](./media/containers-offer-process.png)

As etapas de alto nível para a publicação de uma oferta de contêiner são:

1. Criar a oferta-forneça informações detalhadas sobre a oferta. Essas informações incluem: a descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.
2. Crie os ativos comerciais e técnicos – crie os ativos de negócios (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (as imagens de contêineres hospedadas em um registro de contêiner do Azure.
3. Criar a SKU-crie as SKU associadas à oferta. Um SKU exclusivo é necessário para cada imagem que você pretende publicar.
4. Certificar e publicar a oferta-após a oferta e os ativos técnicos serem concluídos, você pode enviar a oferta. Esse envio inicia o processo de publicação. Durante esse processo, a solução é testada, validada, certificada e, em seguida, "fica ativa" no Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

Antes de considerar essas etapas, você deve atender aos [requisitos técnicos e de negócios](./cpp-prerequisites.md) para publicar um contêiner no Microsoft Azure Marketplace.