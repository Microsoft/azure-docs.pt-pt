---
title: SKUs e planos privados | Azure Marketplace
description: Como usar SKUs particulares para gerenciar a disponibilidade da oferta.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: pabutler
ms.openlocfilehash: eb6eac5eafaeea239bfaf9cf2aface3db659dd57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818826"
---
<a name="private-skus-and-plans"></a>SKUs e planos privados
============

As SKUs privadas permitem restringir a disponibilidade de SKUs para clientes específicos. Quando um SKU é marcado como particular, ele não está disponível em nenhum catálogo público, incluindo no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com). No portal do Azure, somente clientes com acesso à SKU podem vê-lo. Além disso, também será solicitado que eles tenham acesso a ofertas privadas.

>[!NOTE]
>As SKUs particulares devem ter novas IDs exclusivas de SKU/plano para evitar qualquer conflito com suas SKUs públicas.

Você pode usar SKUs particulares para lidar com os seguintes cenários:

1.  Publique o software que você deseja disponibilizar apenas publicamente a clientes específicos e não disponível publicamente.
2.  Publique variações de software público a um preço personalizado para clientes específicos.
3.  Publique variações de software público com uma descrição e termos personalizados (por meio de uma nova oferta).

Se você quiser alterar o preço, poderá reutilizar os discos de outra SKU na mesma oferta. Com SKUs particulares, você não precisa reenviar discos entre SKUs.

<a name="mark-a-sku-private"></a>Marcar um SKU privado
---------------------

Para marcar uma SKU como particular, alterne a opção perguntando se a SKU é privada:

![Marcar uma SKU como privada](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Você pode reutilizar os discos em outra SKU e modificar o preço ou a descrição. Para reutilizar os discos, selecione **Sim** como uma resposta para o prompt "este SKU reutiliza imagens de um SKU público".

Se a SKU for marcada como privada e a oferta tiver outras SKUs com discos reutilizáveis, será necessário indicar que a SKU reutiliza os discos de outra SKU. Também é necessário especificar o público-alvo para o SKU privado.

>[!NOTE]
>Depois de publicado, um SKU público não pode ser tornado privado.

<a name="select-an-image"></a>Selecionar uma imagem
------------------

Você pode fornecer novos discos para a SKU privada ou reutilizar os mesmos discos já fornecidos em outra SKU, modificando apenas o preço ou a descrição. Para reutilizar os discos, selecione **Sim** como uma resposta para o prompt "este SKU reutiliza a imagem de um SKU público".

![Indicar reutilização de imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Depois de confirmar que o SKU reutiliza as imagens, selecione o SKU de origem ou *base* para as imagens:

![Selecionar uma imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Quando você publica a oferta, as imagens da SKU selecionada seriam disponibilizadas na ID de SKU particular com as taxas/termos personalizados. A SKU privada só estaria visível para o público-alvo.

Para atualizações de imagem, você só precisaria atualizar a imagem do SKU subjacente. Nos bastidores, a imagem do SKU privado também será atualizada automaticamente. Da mesma forma, se você excluir a imagem do SKU subjacente, a imagem também será removida da SKU privada.

<a name="restricting-the-audience"></a>Restringindo o público-alvo
------------------------

As ofertas privadas podem ser encontradas e implantadas somente por usuários direcionados.
Atualmente, há suporte para direcionar usuários usando IDs de assinatura.

Essas assinaturas podem ser inseridas por meio de um formulário de entrada manual **para até 10 assinaturas**, ou carregando um arquivo CSV, o que permite **até 20.000 assinaturas**.

Entrada manual para público restrito:

![Restringir o público manualmente](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Upload de CSV para público-alvo restrito:

![Usar CSV para restringir o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Conteúdo do arquivo CSV de exemplo:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando você alterna da entrada manual para a exibição de carregamento de CSV ou de CSV para entrada manual, a lista antiga de IDs de assinatura com acesso à SKU não é mantida. Um aviso é exibido e a lista só é substituída quando você salva a oferta.

<a name="managing-private-audiences"></a>Gerenciando públicos privados
-------------------------

**Para atualizar o público sem publicar novamente a oferta inteira, você faz as alterações de público que desejar (usando a interface do usuário ou a API) e, em seguida, inicia a ação "sincronizar audiências privadas".**

Se seu público-alvo for de 10 ou menos assinaturas, você poderá gerenciá-lo totalmente usando a interface do usuário do CPP.

Se seu público-alvo tiver mais de 10 assinaturas, você poderá gerenciá-lo usando um arquivo CSV que pode ser carregado na interface do usuário do CPP ou usando a API.

Se você estiver usando a API e não quiser manter um arquivo CSV, poderá gerenciar o público diretamente usando a API de acordo com as instruções abaixo.

> [!NOTE]
> Use a ID de assinatura do Azure (planos e SKUs) ou a ID de locatário (somente planos) para adicionar um público à sua oferta privada.

###  <a name="managing-subscriptions-with-the-api"></a>Gerenciando assinaturas com a API

Você pode usar a API para carregar um CSV ou gerenciar seu público diretamente (sem usar um CSV). Em geral, você simplesmente precisa recuperar sua oferta, atualizar o objeto `restrictedAudience` e enviar essas alterações de volta para sua oferta a fim de adicionar ou remover membros públicos.

Veja como atualizar de forma programática sua lista de audiências:

1. [Recupere seus dados de oferta](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Encontre objetos de audiência restritos em cada SKU da oferta usando esta consulta JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Atualize os objetos público restritos para sua oferta.

    **Se você carregou originalmente a lista de assinaturas para sua oferta privada do arquivo CSV:**

    Seus objetos *restrictedAudience* terão a seguinte aparência.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Para cada objeto público restrito:

    a. Baixe o conteúdo de `restrictedAudience.uploadedCsvUri`. O conteúdo é simplesmente um arquivo CSV com cabeçalhos. Por exemplo:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Adicione ou exclua assinaturas no arquivo CSV baixado, conforme necessário.

    c. Carregue o arquivo CSV atualizado em um local, como o [armazenamento de BLOBs do Azure](../../storage/blobs/storage-blobs-overview.md) ou o [onedrive](https://onedrive.live.com), e crie um link somente leitura para o arquivo. Esse será o novo *SasUrl*.

    d. Atualize a chave de `restrictedAudience.uploadedCsvUri` com seu novo *SasUrl*.

    **Se você inseriu manualmente a lista original de assinaturas para sua oferta privada do Portal do Cloud Partner:**

    Seus objetos *restrictedAudience* serão mais ou menos assim:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Para cada objeto público restrito, adicione ou exclua entradas na lista de `restrictedAudience.manualEntries`, conforme necessário.

4. Ao concluir a atualização de todos os objetos *restrictedAudience* para cada SKU de sua oferta privada, [atualize a oferta](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Com isso, sua lista de audiências atualizada agora está em vigor.

<a name="previewing-private-offers"></a>Visualizando ofertas privadas
-------------------------

Durante a etapa de visualização/preparo, somente as assinaturas de visualização de nível de oferta poderão acessar a SKU. Neste estágio de teste, você pode visualizar a oferta como ela seria exibida para os clientes de destino.

Assinaturas de visualização de nível de oferta para acessar ofertas de preparo:

![Visualizar IDs de assinatura](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Depois que a oferta estiver ativa, somente as assinaturas de público restrito (inseridas por meio de entrada manual ou CSV) poderão exibir e implantar o SKU privado. É recomendável que você **sempre inclua suas próprias assinaturas no público restrito** para o SKU privado para fins de validação.

>[!NOTE]
>Para fins de depuração, as equipes de suporte e engenharia da Microsoft também terão acesso a essas ofertas privadas.
