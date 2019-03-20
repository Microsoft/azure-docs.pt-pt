---
title: Atualizar uma módulo oferta existente do Azure IoT Edge | Documentos da Microsoft
description: Como atualizar uma oferta de módulo do IoT Edge existente no Azure Marketplace.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ca7bed26d91c28304638e85d6da93708bfcfcada
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170314"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Atualizar uma oferta de módulo do IoT Edge existente

Este artigo percorre os diferentes aspectos da atualizar a sua oferta de módulo do IoT Edge no [Cloud Partner Portal](https://cloudpartner.azure.com/) e, em seguida, republicar a oferta.

Há vários motivos por que razão poderá querer atualizar a sua oferta, tais como:

-  Adicionar uma nova versão de imagem do módulo do IoT Edge para SKUs existentes.
-  A adicionar os novos SKU.
-  A atualizar os metadados do marketplace para a oferta ou SKUs individuais.

Para ajudá-lo a essas modificações, o portal oferece-a **Compare** e **histórico** funcionalidades.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Alterações unpermitted a oferta de módulo do IoT Edge ou SKU

Existem atributos de uma oferta de módulo do IoT Edge ou o SKU que não pode ser alterado depois da oferta está em direto no Azure Marketplace. Não é possível alterar as seguintes definições:

-  **ID da oferta** e **ID de publicador** da oferta
-  **ID do SKU** dos SKUs existentes
-  Versão etiquetas, por exemplo: `1.0.1`
-  Modelo de faturação/licença é alterado para SKUs existentes

## <a name="common-update-operations"></a>Operações de atualização comuns

As seguintes operações de atualização são comuns.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Atualizar a versão de imagem de módulo do IoT Edge para um SKU

É comum para uma imagem de módulo do IoT Edge a ser atualizado periodicamente com patches de segurança, funcionalidades adicionais e assim por diante. Neste cenário, que pretende atualizar a imagem de módulo do IoT Edge que o SKU faz referência ao utilizar os seguintes passos:

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas**, localize a oferta que pretende atualizar.

3.  Na **SKUs** separador, selecione o SKU associado à imagem de módulo do IoT Edge para atualizar.

4.  Sob **imagem de módulo Edge**, selecione **+ nova versão da imagem** para adicionar uma nova imagem de módulo do IoT Edge.

5.  Fornecer o novo módulo do IoT Edge **versões de imagem**. A versão da imagem tem de cumprir as mesmas diretrizes de marcas como versões anteriores. Etiquetas de versão devem ter o formato x.y. z, em que X, Y e Z são números inteiros. Certifique-se de que a nova versão que fornece é maior do que as versões anteriores tudo.

6.  Selecione **publicar** para iniciar o fluxo de trabalho para publicar a nova versão do módulo do IoT Edge no Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar um novo SKU

Utilize os seguintes passos para disponibilizar um SKU de novo para a sua oferta: 

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas**, localize a oferta que pretende atualizar.

3.  Sob o **SKUs** separador, selecione **adicionar novo SKU** e fornecer uma **ID de SKU** na janela de pop-up.

4.  Voltar a publicar o módulo do IoT Edge através dos passos descritos em [publique um módulo do IoT Edge no Azure Marketplace](./cpp-publish-offer.md).

5.  Selecione **publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.


### <a name="update-offer-marketplace-metadata"></a>Atualizar os metadados do marketplace de oferta

Utilize os seguintes passos para atualizar os metadados de marketplace associados a sua oferta. (Por exemplo: da empresa nome, logótipos, etc.)

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas**, localize a oferta que pretende atualizar.

3.  Vá para o **Marketplace** separador. Utilize as instruções no [publique um módulo do IoT Edge no Azure Marketplace](./cpp-publish-offer.md) artigo para fazer alterações de metadados.

4.  Selecione **publicar** para iniciar o fluxo de trabalho para publicar as alterações.

## <a name="compare-feature"></a>Compare recursos

Quando fizer alterações na oferta publicada, pode utilizar o **comparar** funcionalidade para auditar as alterações que fez. 

**Para utilizar a funcionalidade de comparação:**

1.  Em qualquer momento no processo de edição, selecione **comparar** para a sua oferta.

    ![Comparar o botão de funcionalidade](./media/iot-edge-module-compare.png)


2.  Ver versões lado a lado dos ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade do histórico publicação, selecione o **histórico** separador na navegação à esquerda menu barra de Cloud Portal de parceiros. Pode ver as ações de timestamped tomadas durante o tempo de vida das suas ofertas do Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
