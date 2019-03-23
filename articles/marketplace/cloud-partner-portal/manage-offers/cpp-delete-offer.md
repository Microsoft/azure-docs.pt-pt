---
title: Eliminar as ofertas do Marketplace - Azure Marketplace | Documentos da Microsoft
description: Eliminar ofertas no Azure e mercados de AppSource com o Portal de parceiro de Cloud
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: f26597022b89bcd113ee250db75a842c634273eb
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371388"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Eliminar ofertas do Azure Marketplace e AppSource ou SKUs

Por vários motivos, pode decidir retirar a oferta do seu Microsoft marketplace, que pode assumir duas formas:

- *Remoção da oferta* garante que os novos clientes já não podem comprar ou implementar a sua oferta, mas não afeta os clientes existentes, quem tem de suportar, de acordo com seu contrato de licença e leis pertinentes. 
- *Cessação da oferta* é o processo de extinção do serviço e/ou contrato de licenciamento entre e seus clientes existentes. 

Documentação de orientação e as políticas relacionadas com a remoção e extinção da oferta são regidas pelos [contrato de publicador do Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) (secção 7) e o [políticas de participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (secção [ Remoção e suspensão da oferta](https://docs.microsoft.com/en-us/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Fala este artigo sobre os diferentes suportadas cenários de exclusão e os passos necessários para executar cada um deles.  

> [!NOTE]
> Pode eliminar uma oferta que não tenha sido publicada ao selecionar o **elimine** botão na barra de ferramentas a **Editor** separador.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Eliminar um SKU publicado no Azure Marketplace

Pode eliminar um SKU publicado no Azure Marketplace através dos seguintes passos:

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2.  Na **todas as ofertas** , selecione a sua oferta.  A oferta deve ser exibida no **Editor** separador.
3.  Na barra de ferramentas da lado esquerda, selecione o **SKUs** separador. 
4.  Selecione o SKU que pretende eliminar e clique nas **eliminar** botão.
5.  [Voltar a publicar](./cpp-publish-offer.md) a oferta no Azure Marketplace.

Depois da oferta modificada é publicada no Azure Marketplace, o SKU selecionado já não será apresentado no Azure Marketplace e do portal do Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Reverter para uma versão anterior de SKU

Pode eliminar a versão atual de um SKU publicada no Azure Marketplace através dos passos aqui. Quando o processo estiver concluído, o SKU é revertido para a versão anterior.

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Na **todas as ofertas** , selecione a sua oferta.  A oferta deve ser exibida no **Editor** separador.
3. Na barra de ferramentas da lado esquerda, selecione o **SKUs** separador. 
4. Elimine a versão mais recente do recurso de solução associados da lista de versões de disco.  Consoante o tipo de oferta, este campo pode ser **versão de disco**, **versões de pacotes**, ou recurso semelhante. 
5. [Voltar a publicar](./cpp-publish-offer.md) a oferta no Azure Marketplace.

Depois da oferta modificada é publicada no theAzure Marketplace, a versão atual do SKU listado já não será listada. no Azure Marketplace e o portal do Azure.  O SKU é revertido para a versão anterior.


## <a name="delete-a-live-offer"></a>Eliminar uma oferta em direto

Existem vários procedimentos, empresa e os aspectos legais para remover uma oferta em direto. Siga os passos seguintes para obter documentação de orientação da Equipe do suporte para remover uma oferta em direto no Azure Marketplace:

1.  Emitir um pedido de suporte através do [criar um incidente](https://go.microsoft.com/fwlink/?linkid=844975) página ou ao clicar em **suportar** no canto superior direito do [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Selecione o tipo de oferta específico no **tipo de problema** lista e selecione **remover uma oferta publicada** no **categoria** lista.

3.  Submeta o pedido.

A equipa de suporte orienta-o ao longo do processo de eliminação de oferta.

> [!NOTE]
> A eliminar uma oferta (ou SKU) não irá afetar a compra atual dessa oferta (ou SKU). Estas compras irão continuar a trabalhar como antes. No entanto, ofertas eliminadas ou SKUs não estarão disponíveis para quaisquer compras futuras.


## <a name="next-steps"></a>Passos Seguintes

Depois de se familiarizar com as operações básicas, utilizadas para gerir ofertas, está pronto para criar uma instância de um Microsoft [oferta do marketplace](../cpp-marketplace-offers.md).
