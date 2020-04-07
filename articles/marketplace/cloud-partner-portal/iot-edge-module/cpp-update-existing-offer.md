---
title: Atualize uma oferta de módulo Si o T.T Edge existente Mercado Azure
description: Como atualizar uma oferta de móduloI OT Edge existente no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 821007e40eaa91c98f157ee0b28fe01f37e3cbc9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743957"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Atualizar uma oferta de módulo IoT Edge existente

>[!Important]
>A partir de 30 de março de 2020, começaremos a movimentar a gestão do seu módulo IoT Edge oferece ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de módulo IoT Edge](https://aka.ms/AzureCreateIoT) para gerir as suas ofertas migratórias.

Este artigo atravessa os diferentes aspetos da atualização da sua oferta de módulos IoT Edge no Portal do [Parceiro cloud](https://cloudpartner.azure.com/) e, em seguida, reedita a oferta.

Existem várias razões pelas quais poderá querer atualizar a sua oferta, tais como:

-  Adicionar uma nova versão de imagem do módulo IoT Edge às SKUs existentes.
-  Adicionando novas SKUs.
-  Atualizar os metadados do mercado para a oferta ou SKUs individuais.

Para o ajudar nestas modificações, o portal oferece as funcionalidades **Compare** e **History.**  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Alterações não autorizadas na oferta de módulos IoT Edge ou SKU

Existem atributos de uma oferta de móduloIO T Edge ou SKU que não pode ser alterado após a oferta estar ao vivo no Azure Marketplace. Não é possível alterar as seguintes definições:

-  **Oferta ID** e **Id** editor da oferta
-  **Identificação SKU** das SKUs existentes
-  Etiquetas de versão, por exemplo:`1.0.1`
-  Alterações no modelo de faturação/licença às SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As seguintes operações de atualização são comuns.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Atualize a versão de imagem do módulo IoT Edge para um SKU

É comum que uma imagem do módulo IoT Edge seja periodicamente atualizada com patches de segurança, funcionalidades adicionais, e assim por diante. Neste cenário, pretende atualizar a imagem do módulo IoT Edge que o seu SKU refere utilizando os seguintes passos:

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas,** encontre a oferta que pretende atualizar.

3.  No separador **SKUs,** selecione o SKU associado à imagem do módulo IoT Edge para atualizar.

4.  Sob a imagem do **módulo Edge,** selecione **+ Versão nova de imagem** para adicionar uma nova imagem do módulo IoT Edge.

5.  Forneça as novas **versões**de imagem do módulo IoT Edge. A versão de imagem precisa de seguir as mesmas diretrizes de tags que as versões anteriores. As etiquetas de versão devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que fornece é maior do que todas as versões anteriores.

6.  Selecione **Publicar** para iniciar o fluxo de trabalho para publicar a sua nova versão do módulo IoT Edge para o Mercado Azure.

### <a name="add-a-new-sku"></a>Adicione um novo SKU

Utilize os seguintes passos para disponibilizar um novo SKU para a sua oferta: 

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas,** encontre a oferta que pretende atualizar.

3.  Sob o separador **SKUs,** selecione **Adicionar novo SKU** e fornecer um **ID SKU** na janela pop-up.

4.  Republique o módulo IoT Edge utilizando os passos descritos na [Publicação de um módulo IoT Edge para o Azure Marketplace](./cpp-publish-offer.md).

5.  Selecione **Publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.


### <a name="update-offer-marketplace-metadata"></a>Atualizar oferecem metadados de mercado

Utilize os seguintes passos para atualizar os metadados do mercado associados à sua oferta. (Por exemplo: nome da empresa, logotipos, etc.)

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas,** encontre a oferta que deseja atualizar.

3.  Vá ao separador **Marketplace.** Utilize as instruções no [módulo Publicar um módulo IoT Edge para](./cpp-publish-offer.md) o artigo do Azure Marketplace para efazer alterações de metadados.

4.  Selecione **Publicar** para iniciar o fluxo de trabalho para publicar as suas alterações.

## <a name="compare-feature"></a>Comparar funcionalidade

Quando fizer alterações numa oferta publicada, pode utilizar a funcionalidade **Compare** para auditar as alterações que fez. 

**Para utilizar a função Compare:**

1.  Em qualquer ponto do processo de edição, selecione **Compare** para a sua oferta.

    ![Comparar botão de funcionalidade](./media/iot-edge-module-compare.png)


2.  Veja as versões lado a lado de ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>História das Ações Editoriais

Para ver a atividade de publicação histórica, selecione o separador **History** na barra de menus de navegação esquerda do Cloud Partner Portal. Pode ver as ações timestamped tomadas durante a vida das suas ofertas do Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
