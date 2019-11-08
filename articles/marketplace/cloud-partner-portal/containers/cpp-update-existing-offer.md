---
title: Atualizar uma oferta de contêineres do Azure existente | Azure Marketplace
description: Como atualizar uma oferta de contêiner existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 40cd375b11eb5b7ad5943fea9839b6339a7d002f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823074"
---
# <a name="update-an-existing-container-offer"></a>Atualizar uma oferta de contêiner existente

Este artigo percorre os diferentes aspectos da atualização de sua oferta de contêiner no [portal do Cloud Partner](https://cloudpartner.azure.com/).

Há várias razões pelas quais você talvez queira atualizar sua oferta, como:

-  Adicionar uma nova versão de imagem de contêiner a SKUs existentes.
-  Adicionando novas SKUs.
-  Atualizando os metadados do Marketplace para a oferta ou SKUs individuais.

Para ajudá-lo nessas modificações, o portal fornece os recursos de **comparação** e **histórico** .  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Alterações não permitidas em uma oferta de contêiner ou SKU

Há atributos de uma oferta de contêiner ou SKU que não podem ser alterados depois que a oferta está ativa no Azure Marketplace. Não é possível alterar as seguintes configurações:

-  ID da **oferta** e **ID do editor** da oferta
-  **ID de SKU** de SKUs existentes
-  Marcas de versão, por exemplo: `1.0.1`
-  Alterações no modelo de cobrança/licença para SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As seguintes operações de atualização são comuns.

### <a name="update-container-image-version-for-a-sku"></a>Atualizar versão da imagem de contêiner para uma SKU

É comum que uma imagem de contêiner seja atualizada periodicamente com patches de segurança, recursos adicionais e assim por diante. Nesse cenário, você deseja atualizar a imagem de contêiner que seu SKU faz referência usando as seguintes etapas:

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **todas as ofertas**, localize a oferta que você deseja atualizar.
3. Na guia **SKUs** , selecione a SKU associada à imagem de contêiner a ser atualizada.
4. Em **imagem de contêiner**, selecione **+ nova versão de imagem** para adicionar uma nova imagem de contêiner.
5. Forneça as novas **versões de imagem**de contêiner. A versão da imagem precisa seguir as mesmas diretrizes de marcas que as versões anteriores. As marcas de versão devem estar no formato X. Y. Z, em que X, Y e Z são inteiros. Verifique se a nova versão que você fornece é maior do que todas as versões anteriores.
6. Selecione **publicar** para iniciar o fluxo de trabalho para publicar sua nova versão de imagem de contêiner no Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar uma nova SKU

Use as etapas a seguir para disponibilizar uma nova SKU para sua oferta:

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **todas as ofertas**, localize a oferta que você deseja atualizar.
3. Na guia **SKUs** , selecione **Adicionar nova SKU** e forneça uma **ID de SKU** na janela pop-up.
4. Republique o contêiner usando as etapas descritas na [oferta publicar contêiner](./cpp-publish-offer.md).
5. Selecione **publicar** para iniciar o fluxo de trabalho para publicar o novo SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do Marketplace da oferta

Use as etapas a seguir para atualizar os metadados do Marketplace associados à sua oferta. (Por exemplo: nome da empresa, logotipos e etc.)

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **todas as ofertas**, encontre a oferta que você gostaria de atualizar.
3. Vá para a guia **Marketplace** . Use as instruções no artigo [publicar oferta de contêiner](./cpp-publish-offer.md) da oferta para fazer alterações de metadados.
4. Selecione **publicar** para iniciar o fluxo de trabalho para publicar suas alterações.

## <a name="compare-feature"></a>Comparar recurso

Ao fazer alterações em uma oferta publicada, você pode usar o recurso **comparar** para auditar as alterações feitas.

### <a name="to-use-the-compare-feature"></a>Para usar o recurso comparar:

1. Em qualquer ponto do processo de edição, selecione comparar para sua oferta.
2. Examine as versões lado a lado dos ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade de publicação histórica, selecione a guia **histórico** na barra de menus de navegação à esquerda de portal do Cloud Partner. Você pode ver as ações de carimbo de data/hora executadas durante o tempo de vida de suas ofertas do Azure Marketplace.