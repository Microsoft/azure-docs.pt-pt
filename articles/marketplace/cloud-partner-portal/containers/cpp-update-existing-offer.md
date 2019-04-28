---
title: Atualizar uma oferta de contentores do Azure existente | Documentos da Microsoft
description: Como atualizar uma oferta existente do contentor no Azure Marketplace.
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
ms.topic: article
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 2b568717b6656fb9ae15e9a6dbd27441689c4372
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472711"
---
# <a name="update-an-existing-container-offer"></a>Atualizar uma oferta existente do contentor

Este artigo percorre os diferentes aspectos da atualizar a sua oferta de contentor no [Cloud Partner Portal](https://cloudpartner.azure.com/).

Há vários motivos por que razão poderá querer atualizar a sua oferta, tais como:

-  Adicionar uma nova versão de imagem de contentor para SKUs existentes.
-  A adicionar os novos SKU.
-  A atualizar os metadados do marketplace para a oferta ou SKUs individuais.

Para ajudá-lo a essas modificações, o portal fornece o **Compare** e **histórico** funcionalidades.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Unpermitted alterações a uma oferta de contentor ou SKU

Existem atributos de uma oferta de contentor ou SKU que não pode ser alterado depois da oferta está em direto no Azure Marketplace. Não é possível alterar as seguintes definições:

-  **ID da oferta** e **ID de publicador** da oferta
-  **ID do SKU** dos SKUs existentes
-  Versão etiquetas, por exemplo: `1.0.1`
-  Modelo de faturação/licença é alterado para SKUs existentes

## <a name="common-update-operations"></a>Operações de atualização comuns

As seguintes operações de atualização são comuns.

### <a name="update-container-image-version-for-a-sku"></a>Atualizar a versão de imagem de contentor para um SKU

É comum para uma imagem de contentor ser atualizado periodicamente com patches de segurança, funcionalidades adicionais e assim por diante. Neste cenário, que pretende atualizar a imagem de contentor que o SKU faz referência ao utilizar os seguintes passos:

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Na **SKUs** separador, selecione o SKU associado à imagem de contentor para atualizar.
4. Sob **imagem de contentor**, selecione **+ nova versão da imagem** para adicionar uma nova imagem de contentor.
5. Fornecer o novo contentor **versões de imagem**. A versão da imagem tem de cumprir as mesmas diretrizes de marcas como versões anteriores. Etiquetas de versão devem ter o formato x.y. z, em que X, Y e Z são números inteiros. Certifique-se de que a nova versão que fornece é maior do que as versões anteriores tudo.
6. Selecione **publicar** para iniciar o fluxo de trabalho para publicar a nova versão de imagem de contentor no Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar um novo SKU

Utilize os seguintes passos para disponibilizar um SKU de novo para a sua oferta:

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Sob o **SKUs** separador, selecione **adicionar novo SKU** e fornecer uma **ID de SKU** na janela de pop-up.
4. Voltar a publicar o contentor utilizando os passos descritos em [publicar oferta de contentor](./cpp-publish-offer.md).
5. Selecione **publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar os metadados do marketplace de oferta

Utilize os seguintes passos para atualizar os metadados de marketplace associados a sua oferta. (Por exemplo: da empresa, nome, logótipos e etc.)

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Vá para o **Marketplace** separador. Utilize as instruções no [publicar oferta de contentor](./cpp-publish-offer.md) oferecem artigo para fazer alterações de metadados.
4. Selecione **publicar** para iniciar o fluxo de trabalho para publicar as alterações.

## <a name="compare-feature"></a>Compare recursos

Quando fizer alterações a uma oferta publicada, pode utilizar o **comparar** funcionalidade para auditar as alterações que fez.

### <a name="to-use-the-compare-feature"></a>Para utilizar a funcionalidade de comparação:

1. Em qualquer momento no processo de edição, selecione a comparação para a sua oferta.
2. Ver versões lado a lado dos ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade do histórico publicação, selecione o **histórico** separador na navegação à esquerda menu barra de Cloud Portal de parceiros. Pode ver as ações de timestamped tomadas durante o tempo de vida das suas ofertas do Azure Marketplace.