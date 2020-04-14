---
title: Oferta de imagem de Contentores Azure [ Mercado Azure
description: Visão geral do processo de publicação de uma oferta de contentores no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: cd9f98d42efcb35dbab4f3c0a06c5a11360e36b6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270354"
---
# <a name="containers"></a>Contentores

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de contentores Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de recipiente azure](https://aka.ms/CreateContainerOffer) para gerir as suas ofertas migratórias.

<table> <tr> <td>Esta secção explica como publicar uma imagem de contentor no <a href="https://azuremarketplace.microsoft.com">Azure Marketplace.</a>  
O tipo de oferta de contentores suporta imagens de contentores Docker aprovisionadas como instâncias <a href="https://docs.microsoft.com/azure/aks/index">de serviço Azure Kubernetes</a> ou Instâncias de <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Contentores Azure</a> e hospedada num repositório de registo de <a href="https://docs.microsoft.com/azure/container-registry">contentores Azure.</a> </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Oferecer componentes

Esta secção descreve os elementos da publicação de um contentor, e destina-se a ser um guia para a editora para o Mercado Azure. A publicação está dividida nas seguintes partes principais:

- [Pré-requisitos](./cpp-prerequisites.md) - lista os requisitos técnicos e empresariais antes de criar ou publicar uma oferta de contentores.
- [Criar a oferta](./cpp-create-offer.md) - lista os passos necessários para criar uma nova entrada de oferta de contentores usando o Portal do Parceiro cloud.
- [Preparar os ativos técnicos](./cpp-create-technical-assets.md) - como criar os ativos técnicos para uma solução de contentores como oferta no Mercado Azure.
- [Publique a oferta](./cpp-publish-offer.md) - como submeter a oferta para publicação no Mercado Azure.

## <a name="container-publishing-process"></a>Processo de publicação de contentores

O diagrama que se segue ilustra os passos de alto nível na publicação de uma oferta vm.
![Passos para publicar uma oferta](./media/containers-offer-process.png)

Os passos de alto nível para a publicação de uma oferta de contentores são:

1. Criar a oferta - Forneça informações detalhadas sobre a oferta. Estas informações incluem: descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.
2. Criar os ativos empresariais e técnicos - Criar os ativos empresariais (documentos legais e materiais de marketing) e os ativos técnicos para a solução associada (as imagens de contentores alojadas num Registo de Contentores Azure.
3. Criar o SKU - Criar o SKU(s) associado à oferta. É necessário um SKU único para cada imagem que planeia publicar.
4. Certifique e publique a oferta - Depois de concluída a oferta e os ativos técnicos, pode submeter a oferta. Esta submissão inicia o processo de publicação. Durante este processo, a solução é testada, validada, certificada e depois "vai ao vivo" no Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

Antes de considerar estas etapas, deve cumprir os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para a publicação de um contentor no Microsoft Azure Marketplace.
