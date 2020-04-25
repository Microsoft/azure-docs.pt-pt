---
title: Atualizar uma oferta de contentores Azure existentes [ Mercado Azure
description: Como atualizar uma oferta de contentores existente no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: cc378dcc1d5f777f4e81825e2f99dedd37a87f96
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143809"
---
# <a name="update-an-existing-container-offer"></a>Atualizar uma oferta de contentores existente

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de contentores Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de recipiente azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) para gerir as suas ofertas migratórias.

Este artigo atravessa os diferentes aspetos da atualização da sua oferta de contentores no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/)

Existem várias razões pelas quais poderá querer atualizar a sua oferta, tais como:

-  Adicionar uma nova versão de imagem de recipiente às SKUs existentes.
-  Adicionando novas SKUs.
-  Atualizar os metadados do mercado para a oferta ou SKUs individuais.

Para o ajudar nestas modificações, o portal fornece as funcionalidades **Compare** e **History.**  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Alterações não autorizadas a uma oferta de contentores ou SKU

Existem atributos de uma oferta de contentores ou SKU que não podem ser alterados após a oferta estar ao vivo no Azure Marketplace. Não é possível alterar as seguintes definições:

-  **Oferta ID** e **Id** editor da oferta
-  **Identificação SKU** das SKUs existentes
-  Etiquetas de versão, por exemplo:`1.0.1`
-  Alterações no modelo de faturação/licença às SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As seguintes operações de atualização são comuns.

### <a name="update-container-image-version-for-a-sku"></a>Atualizar versão de imagem de recipiente para um SKU

É comum que uma imagem de contentor seja periodicamente atualizada com patches de segurança, funcionalidades adicionais, e assim por diante. Neste cenário, pretende atualizar a imagem do recipiente que o seu SKU refere utilizando os seguintes passos:

1. Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)
2. Em **todas as ofertas,** encontre a oferta que pretende atualizar.
3. No separador **SKUs,** selecione o SKU associado à imagem do recipiente para atualizar.
4. Sob **a imagem do Recipiente,** selecione + Versão nova de **imagem** para adicionar uma nova imagem de recipiente.
5. Forneça as novas **versões**de imagem do recipiente. A versão de imagem precisa de seguir as mesmas diretrizes de tags que as versões anteriores. As etiquetas de versão devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que fornece é maior do que todas as versões anteriores.
6. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar a sua nova versão de imagem de contentor para o Mercado Azure.

### <a name="add-a-new-sku"></a>Adicione um novo SKU

Utilize os seguintes passos para disponibilizar um novo SKU para a sua oferta:

1. Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)
2. Em **todas as ofertas,** encontre a oferta que pretende atualizar.
3. Sob o separador **SKUs,** selecione **Adicionar novo SKU** e fornecer um **ID SKU** na janela pop-up.
4. Republique o recipiente utilizando as etapas descritas na oferta do [contentor Publicar](./cpp-publish-offer.md).
5. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar oferecem metadados de mercado

Utilize os seguintes passos para atualizar os metadados do mercado associados à sua oferta. (Por exemplo: nome da empresa, logotipos e etc.)

1. Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)
2. Em **todas as ofertas,** encontre a oferta que gostaria de atualizar.
3. Vá ao separador **Marketplace.** Utilize as instruções no artigo de oferta de oferta de oferta de [contentores Da Publicação](./cpp-publish-offer.md) para efazer alterações de metadados.
4. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar as suas alterações.

## <a name="compare-feature"></a>Comparar funcionalidade

Quando efaz alterações a uma oferta publicada, pode utilizar a funcionalidade **Compare** para auditar as alterações que fez.

### <a name="to-use-the-compare-feature"></a>Para utilizar a função Compare:

1. Em qualquer ponto do processo de edição, selecione Compare para a sua oferta.
2. Veja as versões lado a lado de ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>História das ações editoriais

Para ver a atividade de publicação histórica, selecione o separador **History** na barra de menus de navegação esquerda do Cloud Partner Portal. Pode ver as ações timestamped tomadas durante a vida das suas ofertas do Azure Marketplace.
