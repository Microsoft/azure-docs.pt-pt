---
title: Atualizar uma oferta de aplicação do Azure existente | Documentos da Microsoft
description: Como atualizar uma oferta de aplicação do Azure existente no Azure Marketplace.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1019c06afbdab0a6b0e172133da316aea01a5504
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216905"
---
# <a name="update-an-existing-azure-application-offer"></a>Atualizar uma oferta de aplicação do Azure existente

Existem vários tipos de atualizações que pode querer fazer sua oferta depois foi publicado e está em direto. Qualquer alteração feita para a nova versão da sua oferta deve ser guardada e republicada tê-lo a refletir no Marketplace. Este artigo percorre os diferentes aspectos da atualizar a sua oferta de aplicação gerida no [Cloud Partner Portal](https://cloudpartner.azure.com/).

Há vários motivos por que razão poderá querer atualizar a sua oferta, tais como:

- Adicionar uma nova versão de imagem para SKUs existentes.
- A adicionar os novos SKU.
- A atualizar os metadados do marketplace para a oferta ou SKUs individuais.

Para ajudá-lo a essas modificações, o portal fornece o **Compare** e **histórico** funcionalidades.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Unpermitted alterações a uma oferta de aplicação do Azure ou SKU

Existem atributos de uma oferta de contentor ou SKU que não pode ser alterado depois da oferta está em direto no Azure Marketplace. Não é possível alterar as seguintes definições:

- ID de oferta e o ID de publicador da oferta
- ID do SKU de SKUs existentes
- Versão etiquetas, por exemplo: `1.0.1`
- Modelo de faturação/licença é alterado para SKUs existentes

## <a name="common-update-operations"></a>Operações de atualização comuns

As seguintes operações de atualização são comuns.

### <a name="update-image-version-for-a-sku"></a>Atualizar a versão da imagem para uma SKU

É comum para uma imagem a ser atualizado periodicamente com patches de segurança, funcionalidades adicionais e assim por diante. Neste cenário, que pretende atualizar a imagem que o SKU faz referência ao utilizar os seguintes passos:

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Na **SKUs** separador, selecione o SKU associado à imagem para atualizar.
4. Selecione **+ nova versão da imagem** para adicionar uma nova imagem.
5. Fornece as novas versões de imagem. A versão da imagem tem de cumprir as mesmas diretrizes de marcas como versões anteriores. Etiquetas de versão devem ter o formato x.y. z, em que X, Y e Z são números inteiros. Certifique-se de que a nova versão que fornece é maior do que as versões anteriores tudo.
6. Selecione **publicar** para iniciar o fluxo de trabalho para publicar a nova versão de imagem de contentor no Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar um novo SKU

Utilize os seguintes passos para disponibilizar um SKU de novo para a sua oferta:

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Sob o **SKUs** separador, selecione **adicionar novo SKU** e fornecer uma **ID de SKU** na janela de pop-up.
4. Voltar a publicar a oferta através dos passos descritos em [oferta de aplicação do Azure de publicar](./cpp-publish-offer.md).
5. Selecione **publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar os metadados do marketplace de oferta

Utilize os seguintes passos para atualizar os metadados de marketplace associados a sua oferta. (Por exemplo: da empresa nome, logótipos, etc.)

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Vá para o **Marketplace** separador. Utilize as instruções em [oferta de aplicação do Azure de publicar](./cpp-publish-offer.md) para fazer alterações de metadados.
4. Selecione **publicar** para iniciar o fluxo de trabalho para publicar as alterações.
 
>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](../../cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="deleting-an-existing-offer"></a>A eliminar uma oferta existente

Pode optar por remover a oferta do Marketplace. A eliminar uma oferta não afeta a compra atual dessa oferta. Essas compras do cliente irão continuar a trabalhar como antes. No entanto, a oferta não estarão disponível para qualquer novas compras após a eliminação foi concluída.

A Extinção da Oferta é o processo de extinção do serviço e/ou contrato de licenciamento entre si e os seus clientes existentes.
Documentação de orientação e as políticas relacionadas com a remoção e extinção da oferta são regidas pelo contrato de publicador do Microsoft Marketplace (consultar secção 7) e as políticas de participação (consulte a secção 6.2).

Para obter mais informações, consulte [Delete e oferta/SKU do Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Compare recursos

Quando fizer alterações a uma oferta publicada, pode utilizar a funcionalidade de comparação para auditar as alterações que fez.

Para utilizar a funcionalidade de comparação:

1. Em qualquer momento no processo de edição, selecione a comparação para a sua oferta.
2. Ver versões lado a lado dos ativos de marketing e metadados.

## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade do histórico publicação, selecione o **histórico** separador na navegação à esquerda menu barra de Cloud Portal de parceiros. Pode ver as ações de timestamped tomadas durante o tempo de vida das suas ofertas do Azure Marketplace.

## <a name="next-steps"></a>Passos Seguintes

[Oferta da aplicação do Azure](./cpp-azure-app-offer.md)