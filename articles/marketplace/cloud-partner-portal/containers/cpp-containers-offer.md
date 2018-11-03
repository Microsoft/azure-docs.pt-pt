---
title: Oferta de imagem de contentores do Azure | Documentos da Microsoft
description: Descrição geral do processo de publicação de uma oferta de contentor no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pbutlerm
ms.openlocfilehash: e40e83e16ab2bfd43c3bb5fa38e52a778694e90e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979935"
---
# <a name="containers"></a>Contentores

<table> <tr> <td>Esta secção explica como publicar uma imagem de contentor para o <a href="https://azuremarketplace.microsoft.com">do Azure Marketplace</a>.  
O suporte de tipo de oferta de contentor imagens de contentor do Docker aprovisionadas como <a href="https://docs.microsoft.com/azure/aks/index">serviço Kubernetes do Azure</a> instâncias ou <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> e alojada num <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry </a> repositório. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Componentes de oferta

Esta secção descreve os elementos de publicação de um contentor e destina-se como um guia para o publicador para o Azure Marketplace. A publicação dividido nas seguintes partes principais:

- [Pré-requisitos](./cpp-prerequisites.md) -lista os requisitos técnicos e empresariais antes de criar ou publicar uma oferta de contentor.
- [Criar a oferta](./cpp-create-offer.md) -lista os passos necessários para criar uma nova entrada de oferta de contentor com o Portal de parceiro de Cloud.
- [Preparar os recursos técnicos](./cpp-create-technical-assets.md) -como criar os recursos técnicos para uma solução de contentores como uma oferta no Azure Marketplace.
- [Publicar a oferta](./cpp-publish-offer.md) -como submeter a oferta para publicação no Azure Marketplace.

## <a name="container-publishing-process"></a>Processo de publicação do contentor

O diagrama seguinte ilustra as etapas de alto nível em publicar uma oferta VM.
![Passos para publicar uma oferta](./media/containers-offer-process.png)

Os passos de alto nível para publicar uma oferta de contentor são:

1. Criar a oferta - fornecem informações detalhadas sobre a oferta. Estas informações incluem: a descrição da oferta, as especificações dos ativos, informações de suporte e materiais de marketing.
2. Criar os recursos técnicos e comerciais - criar os ativos empresariais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (as imagens de contentores alojados no Azure Container Registry.
3. Criar o SKU - criar o SKU (s) associado com a oferta. Um SKU exclusivo é necessário para cada imagem que pretende publicar.
4. Certificar e publicar a oferta - depois da oferta e os ativos técnicos são concluídos, pode enviar a oferta. Esta submissão inicia o processo de publicação. Durante este processo, a solução é testada, validado, certified, em seguida, "entrar no ar" no Azure Marketplace.

## <a name="next-steps"></a>Passos Seguintes

Antes de considerar estes passos, tem de cumprir os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para a publicação de um contentor para o Microsoft Azure Marketplace.