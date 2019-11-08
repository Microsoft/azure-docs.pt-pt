---
title: Atualizar uma oferta de aplicativo do Azure existente | Azure Marketplace
description: Como atualizar uma oferta de aplicativo do Azure existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: 6854e445ea63639866e9e39a6afc725237bbc8fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826101"
---
# <a name="update-an-existing-azure-application-offer"></a>Atualizar uma oferta de aplicativo do Azure existente

Há vários tipos de atualizações que você pode querer fazer em sua oferta depois que ela tiver sido publicada e estiver ativa. Qualquer alteração feita na sua nova versão da sua oferta deve ser salva e republicada para que ela reflita no Marketplace. Este artigo percorre os diferentes aspectos da atualização de sua oferta de aplicativo gerenciado no [portal do Cloud Partner](https://cloudpartner.azure.com/).

Há várias razões pelas quais você talvez queira atualizar sua oferta, como:

- Adicionando uma nova versão de imagem a SKUs existentes.
- Adicionando novas SKUs.
- Atualizando os metadados do Marketplace para a oferta ou SKUs individuais.

Para ajudá-lo nessas modificações, o portal fornece os recursos de **comparação** e **histórico** .

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Alterações não permitidas em uma oferta de aplicativo do Azure ou SKU

Há atributos de uma oferta de contêiner ou SKU que não podem ser alterados depois que a oferta está ativa no Azure Marketplace. Não é possível alterar as seguintes configurações:

- ID da oferta e ID do editor da oferta
- ID de SKU de SKUs existentes
- Marcas de versão, por exemplo: `1.0.1`
- Alterações no modelo de cobrança/licença para SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As seguintes operações de atualização são comuns.

### <a name="update-image-version-for-a-sku"></a>Atualizar a versão da imagem para uma SKU

É comum que uma imagem seja atualizada periodicamente com patches de segurança, recursos adicionais e assim por diante. Nesse cenário, você deseja atualizar a imagem que seu SKU faz referência usando as seguintes etapas:

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **todas as ofertas**, localize a oferta que você deseja atualizar.
3. Na guia **SKUs** , selecione a SKU associada à imagem a ser atualizada.
4. Selecione **+ nova versão da imagem** para adicionar uma nova imagem.
5. Forneça as novas versões de imagem. A versão da imagem precisa seguir as mesmas diretrizes de marcas que as versões anteriores. As marcas de versão devem estar no formato X. Y. Z, em que X, Y e Z são inteiros. Verifique se a nova versão que você fornece é maior do que todas as versões anteriores.
6. Selecione **publicar** para iniciar o fluxo de trabalho para publicar sua nova versão de imagem de contêiner no Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar uma nova SKU

Use as etapas a seguir para disponibilizar uma nova SKU para sua oferta:

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **todas as ofertas**, localize a oferta que você deseja atualizar.
3. Na guia **SKUs** , selecione **Adicionar nova SKU** e forneça uma **ID de SKU** na janela pop-up.
4. Republique a oferta usando as etapas descritas em [publicar a oferta de aplicativo do Azure](./cpp-publish-offer.md).
5. Selecione **publicar** para iniciar o fluxo de trabalho para publicar o novo SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do Marketplace da oferta

Use as etapas a seguir para atualizar os metadados do Marketplace associados à sua oferta. (Por exemplo: nome da empresa, logotipos, etc.)

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **todas as ofertas**, encontre a oferta que você gostaria de atualizar.
3. Vá para a guia **Marketplace** . Use as instruções em [publicar a oferta de aplicativo do Azure](./cpp-publish-offer.md) para fazer alterações de metadados.
4. Selecione **publicar** para iniciar o fluxo de trabalho para publicar suas alterações.
 
>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](../../cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

## <a name="deleting-an-existing-offer"></a>Excluindo uma oferta existente

Você pode decidir remover sua oferta do Marketplace. A exclusão de uma oferta não afeta as compras atuais dessa oferta. Essas compras do cliente continuarão a funcionar como antes. No entanto, a oferta não estará disponível para novas compras após a conclusão da exclusão.

A Extinção da Oferta é o processo de extinção do serviço e/ou contrato de licenciamento entre si e os seus clientes existentes.
Diretrizes e políticas relacionadas à remoção e à conclusão da oferta são regidas por Microsoft Marketplace contrato de editor (consulte a seção 7) e as políticas de participação (consulte a seção 6,2).

Para obter mais informações, consulte [excluir e oferta/SKU do Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Comparar recurso

Ao fazer alterações em uma oferta publicada, você pode usar o recurso comparar para auditar as alterações feitas.

Para usar o recurso comparar:

1. Em qualquer ponto do processo de edição, selecione comparar para sua oferta.
2. Examine as versões lado a lado dos ativos de marketing e metadados.

## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade de publicação histórica, selecione a guia **histórico** na barra de menus de navegação à esquerda de portal do Cloud Partner. Você pode ver as ações de carimbo de data/hora executadas durante o tempo de vida de suas ofertas do Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

[Oferta da aplicação do Azure](./cpp-azure-app-offer.md)