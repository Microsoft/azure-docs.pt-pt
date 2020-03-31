---
title: Eliminar ofertas de marketplace [ Mercado Azure
description: Eliminar ofertas nos Mercados Azure e AppSource utilizando o Portal do Parceiro cloud
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286460"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Eliminar ofertas de Marketplace e AppSource do Azure ou SKUs

Por várias razões, pode decidir retirar a sua oferta do seu mercado microsoft, que pode assumir duas formas:

- *A remoção* da oferta garante que os novos clientes podem deixar de adquirir ou implementar a sua oferta, mas não tem qualquer impacto nos clientes existentes, a quem deve apoiar de acordo com o seu contrato de licença e leis pertinentes. 
- *A rescisão* da oferta é o processo de cessação do contrato de serviço e/ou licenciamento entre si e os seus clientes existentes. 

As orientações e políticas relacionadas com a remoção e rescisão da oferta são regidas pelo [Microsoft Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) e pelas Políticas de [Participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (secção [Oferta de suspensão e remoção).](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal) 

Este artigo fala sobre os diferentes cenários de eliminação suportados e os passos necessários para realizar cada um.  

> [!NOTE]
> Pode eliminar uma oferta que não tenha sido publicada selecionando simplesmente o botão **Delete** na barra de ferramentas do separador **Editor.**


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Eliminar um SKU publicado do Azure Marketplace

Pode eliminar um SKU publicado a partir do Azure Marketplace utilizando os seguintes passos:

1.  Inscreva-se no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/)
2.  Na página **All offers,** selecione a sua oferta.  A sua oferta deve ser apresentada no separador **Editor.**
3.  Na barra de ferramentas esquerda, selecione o separador **SKUs.** 
4.  Selecione o SKU que pretende eliminar e clique no botão **Eliminar.**
5.  [Republique](./cpp-publish-offer.md) a oferta para o Azure Marketplace.

Após a publicação da oferta modificada no Azure Marketplace, o SKU selecionado deixará de estar listado no portal Azure Marketplace e Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Recue para uma versão SKU anterior

Pode eliminar a versão atual de um SKU publicado do Azure Marketplace utilizando os passos aqui. Quando o processo estiver concluído, o SKU é revirado para a versão anterior.

1. Inscreva-se no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/)
2. Na página **All offers,** selecione a sua oferta.  A sua oferta deve ser apresentada no separador **Editor.**
3. Na barra de ferramentas esquerda, selecione o separador **SKUs.** 
4. Elimine a versão mais recente do ativo de solução associada da lista de versões em disco.  Dependendo do tipo de oferta, este campo pode ser **versão de disco,** **versões de pacote,** ou um ativo similar. 
5. [Republique](./cpp-publish-offer.md) a oferta para o Azure Marketplace.

Após a publicação da oferta modificada no Mercado Azure, a versão atual do SKU listado deixará de ser listada. no Azure Marketplace e no portal Azure.  O SKU é revirado para a sua versão anterior.


## <a name="delete-a-live-offer"></a>Apagar uma oferta ao vivo

Existem vários aspetos processuais, empresariais e legais para remover uma oferta ao vivo. Siga os seguintes passos para obter orientação da equipa de apoio para remover uma oferta ao vivo do Mercado Azure:

1.  Levante um bilhete de apoio utilizando a página [Criar uma página](https://go.microsoft.com/fwlink/?linkid=844975) de incidentes, ou clicando em **Suporte** no canto superior direito do Portal do [Parceiro cloud](https://cloudpartner.azure.com/).

2.  Selecione o seu tipo de oferta específico na lista do **tipo Problema** e selecione Remover uma **oferta publicada** na lista **de categorias.**

3.  Submeta o pedido.

A equipa de apoio guia-o através do processo de eliminação da oferta.

> [!NOTE]
> A adesão a uma oferta (ou SKU) não afetará as compras correntes dessa oferta (ou SKU). Estas compras continuarão a funcionar como antes. No entanto, as ofertas eliminadas ou as SKUs não estarão disponíveis para futuras compras.


## <a name="next-steps"></a>Passos seguintes

Depois de conhecer as operações básicas utilizadas para gerir ofertas, está pronto para criar uma instância de uma oferta de [mercado](../cpp-marketplace-offers.md)da Microsoft.
