---
title: Excluir ofertas do Marketplace | Azure Marketplace
description: Excluir ofertas nos Marketplaces do Azure e do AppSource usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 6a75fbb0c4b9c364342a406a8076128346943101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826769"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Excluir ofertas ou SKUs do Azure Marketplace e AppSource

Por vários motivos, você pode optar por retirar sua oferta de seu Microsoft Marketplace, que pode ter duas formas:

- A *remoção da oferta* garante que novos clientes não possam mais comprar ou implantar sua oferta, mas não têm impacto sobre os clientes existentes, com os quais você deve dar suporte de acordo com seu contrato de licença e com as leis pertinentes. 
- O *encerramento da oferta* é o processo de encerrar o serviço e/ou o contrato de licenciamento entre você e os clientes existentes. 

Diretrizes e políticas relacionadas à remoção e ao encerramento da oferta são regidas por [Microsoft Marketplace contrato de editor](https://go.microsoft.com/fwlink/?LinkID=699560) e as [políticas de participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (seção [oferecendo suspensão e remoção](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Este artigo fala sobre os diferentes cenários de exclusão com suporte e as etapas necessárias para executar cada um.  

> [!NOTE]
> Você pode excluir uma oferta que não foi publicada simplesmente selecionando o botão **excluir** na barra de ferramentas da guia **Editor** .


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Excluir uma SKU publicada do Azure Marketplace

Você pode excluir uma SKU publicada do Azure Marketplace usando as seguintes etapas:

1.  Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Na página **todas as ofertas** , selecione sua oferta.  Sua oferta deve ser exibida na guia **Editor** .
3.  Na barra de ferramentas à esquerda, selecione a guia **SKUs** . 
4.  Selecione a SKU que você deseja excluir e clique no botão **excluir** .
5.  [Republique](./cpp-publish-offer.md) a oferta no Azure Marketplace.

Depois que a oferta modificada for publicada no Azure Marketplace, o SKU selecionado não será mais listado no Azure Marketplace e portal do Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Reverter para uma versão anterior do SKU

Você pode excluir a versão atual de uma SKU publicada do Azure Marketplace usando as etapas aqui. Quando o processo for concluído, a SKU será revertida para a versão anterior.

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Na página **todas as ofertas** , selecione sua oferta.  Sua oferta deve ser exibida na guia **Editor** .
3. Na barra de ferramentas à esquerda, selecione a guia **SKUs** . 
4. Exclua a versão mais recente do ativo de solução associado da lista de versões de disco.  Dependendo do tipo de oferta, esse campo pode ser **versão do disco**, **versões do pacote**ou ativo semelhante. 
5. [Republique](./cpp-publish-offer.md) a oferta no Azure Marketplace.

Depois que a oferta modificada for publicada no Azure Marketplace, a versão atual da SKU listada não será mais listada. no Azure Marketplace e no portal do Azure.  A SKU é revertida para a versão anterior.


## <a name="delete-a-live-offer"></a>Excluir uma oferta ao vivo

Há vários aspectos legais, de negócios e de procedimentos para remover uma oferta dinâmica. Siga as etapas a seguir para obter diretrizes da equipe de suporte para remover uma oferta dinâmica do Azure Marketplace:

1.  Gere um tíquete de suporte usando a página [criar um incidente](https://go.microsoft.com/fwlink/?linkid=844975) ou clicando em **suporte** no canto superior direito do [portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Selecione o tipo de oferta específico na lista **tipo de problema** e selecione **remover uma oferta publicada** na lista **categoria** .

3.  Envie a solicitação.

A equipe de suporte orienta você pelo processo de exclusão da oferta.

> [!NOTE]
> A exclusão de uma oferta (ou SKU) não afetará as compras atuais dessa oferta (ou SKU). Essas compras continuarão a funcionar como antes. No entanto, ofertas ou SKUs excluídos não estarão disponíveis para compras futuras.


## <a name="next-steps"></a>Passos seguintes

Depois de se familiarizar com as operações básicas usadas para gerenciar ofertas, você estará pronto para criar uma instância de uma [oferta](../cpp-marketplace-offers.md)do Microsoft Marketplace.
