---
title: Atualizar uma oferta de candidatura azure existente [ Mercado Azure
description: Como atualizar uma oferta de aplicação Azure existente no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275956"
---
# <a name="update-an-existing-azure-application-offer"></a>Atualizar uma oferta de aplicação Azure existente

Existem vários tipos de atualizações que você pode querer fazer à sua oferta depois de publicada e está ao vivo. Qualquer alteração que faça para a sua nova versão da sua oferta deve ser guardada e reeditada para que ela reflita no Marketplace. Este artigo atravessa os diferentes aspetos da atualização da sua oferta de aplicação gerida no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/)

Existem várias razões pelas quais poderá querer atualizar a sua oferta, tais como:

- Adicionar uma nova versão de imagem às SKUs existentes.
- Adicionando novas SKUs.
- Atualizar os metadados do mercado para a oferta ou SKUs individuais.

Para o ajudar nestas modificações, o portal fornece as funcionalidades **Compare** e **History.**

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Alterações não autorizadas a uma oferta de aplicação Azure ou SKU

Existem atributos de uma oferta de contentores ou SKU que não podem ser alterados após a oferta estar ao vivo no Azure Marketplace. Não é possível alterar as seguintes definições:

- Oferta ID e Id editor da oferta
- Identificação SKU das SKUs existentes
- Etiquetas de versão, por exemplo:`1.0.1`
- Alterações no modelo de faturação/licença às SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As seguintes operações de atualização são comuns.

### <a name="update-image-version-for-a-sku"></a>Atualizar versão de imagem para um SKU

É comum uma imagem ser atualizada periodicamente com patches de segurança, funcionalidades adicionais, e assim por diante. Neste cenário, pretende atualizar a imagem que o seu SKU refere utilizando os seguintes passos:

1. Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)
2. Em **todas as ofertas,** encontre a oferta que pretende atualizar.
3. No separador **SKUs,** selecione o SKU associado à imagem para atualizar.
4. Selecione **+ Versão nova da imagem** para adicionar uma nova imagem.
5. Forneça as novas versões de imagem. A versão de imagem precisa de seguir as mesmas diretrizes de tags que as versões anteriores. As etiquetas de versão devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que fornece é maior do que todas as versões anteriores.
6. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar a sua nova versão de imagem de contentor para o Mercado Azure.

### <a name="add-a-new-sku"></a>Adicione um novo SKU

Utilize os seguintes passos para disponibilizar um novo SKU para a sua oferta:

1. Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)
2. Em **todas as ofertas,** encontre a oferta que pretende atualizar.
3. Sob o separador **SKUs,** selecione **Adicionar novo SKU** e fornecer um **ID SKU** na janela pop-up.
4. Republique a oferta utilizando os passos descritos na oferta de [aplicação Publish Azure.](./cpp-publish-offer.md)
5. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar oferecem metadados de mercado

Utilize os seguintes passos para atualizar os metadados do mercado associados à sua oferta. (Por exemplo: nome da empresa, logotipos, etc.)

1. Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)
2. Em **todas as ofertas,** encontre a oferta que gostaria de atualizar.
3. Vá ao separador **Marketplace.** Utilize as instruções na oferta de [aplicação Publish Azure](./cpp-publish-offer.md) para efazer alterações de metadados.
4. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar as suas alterações.
 
>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](../../cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

## <a name="deleting-an-existing-offer"></a>Apagar uma oferta existente

Pode decidir retirar a sua oferta do Marketplace. A abater uma oferta não afeta as compras correntes dessa oferta. Essas compras de clientes continuarão a funcionar como antes. No entanto, a oferta não estará disponível para novas compras após a eliminação.

Oferta Rescisão é o processo de cessação do contrato de serviço e/ou licenciamento entre si e os seus clientes existentes.
As orientações e políticas relacionadas com a remoção e rescisão da oferta são regidas pelo Microsoft Marketplace Publisher Agreement (ver Secção 7) e pelas Políticas de Participação (ver Secção 6.2).

Para mais informações, consulte [Delete e offer/SKU do Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Comparar funcionalidade

Quando efaz alterações a uma oferta publicada, pode utilizar a funcionalidade Compare para auditar as alterações que fez.

Para utilizar a função Compare:

1. Em qualquer ponto do processo de edição, selecione Compare para a sua oferta.
2. Veja as versões lado a lado de ativos de marketing e metadados.

## <a name="history-of-publishing-actions"></a>História das ações editoriais

Para ver a atividade de publicação histórica, selecione o separador **History** na barra de menus de navegação esquerda do Cloud Partner Portal. Pode ver as ações timestamped tomadas durante a vida das suas ofertas do Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

[Oferta de candidatura azure](./cpp-azure-app-offer.md)
