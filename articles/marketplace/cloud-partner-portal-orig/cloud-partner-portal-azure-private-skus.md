---
title: SKUs privados e planos / Mercado Azure
description: Como utilizar SKUs privados para gerir a disponibilidade da oferta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: f4f21539979b54d9aba8dcc0e950b2d3f8bf2ea0
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85515418"
---
<a name="private-skus-and-plans"></a>SKUs e planos privados
============

As SKUs privadas permitem-lhe restringir a disponibilidade de SKUs a clientes específicos. Quando um SKU é marcado como privado, não está disponível em nenhum catálogo público, incluindo no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal Azure.](https://portal.azure.com) No portal Azure, apenas os clientes com acesso ao SKU podem vê-lo. Além disso, também lhes seria solicitado que tivessem acesso a ofertas privadas.

>[!NOTE]
>Os SKUs privados devem ter novas identificações exclusivas do SKU/Plan para evitar qualquer conflito com os seus SKUs públicos.

Pode utilizar SKUs privados para lidar com os seguintes cenários:

1.  Publique software que deseja apenas disponível publicamente para clientes específicos e não disponível publicamente.
2.  Publique variações de software público a um preço personalizado para clientes específicos.
3.  Publique variações de software público com uma descrição e termos personalizados (através de nova oferta).

Se quiser apenas alterar o preço, pode reutilizar os discos de outro SKU na mesma oferta. Com SKUs privados, não é preciso reenviar discos através dos SKUs.

<a name="mark-a-sku-private"></a>Marque um SKU privado
---------------------

Para marcar um SKU como privado, alternar a opção perguntando se o SKU é privado:

![Marque um SKU como privado](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Pode reutilizar os discos em outro SKU e modificar o preço ou a descrição. Para reutilizar os discos, selecione **Sim** como resposta ao pedido "Este SKU reutiliza imagens a partir de um SKU público".

Se o SKU estiver marcado como privado e a oferta tiver outros SKUs com discos reutilizáveis, é-lhe exigido que indique que o SKU reutiliza discos de outro SKU. Também é obrigado a especificar o público-alvo para o SKU privado.

>[!NOTE]
>Depois de publicado, um SKU público não pode ser tornado privado.

<a name="select-an-image"></a>Selecione uma imagem
------------------

Pode fornecer novos discos para o SKU privado ou reutilizar os mesmos discos já fornecidos noutro SKU, modificando apenas o preço ou descrição. Para reutilizar os discos, selecione **Sim** como resposta à reação "Esta imagem de reutilização SKU a partir de um SKU público".

![Indicar reutilização de imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Depois de confirmar que o SKU reutiliza imagens, selecione a fonte ou *base* SKU para as imagens:

![Selecione uma imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Ao publicar a oferta, as imagens do SKU selecionado serão disponibilizadas no SKU ID privado com as tarifas/termos personalizados. O SKU privado só seria visível para o público-alvo.

Para atualizações de imagem, só seria necessário atualizar a imagem do SKU subjacente. Nos bastidores, a imagem do SKU privado também será atualizada automaticamente. Da mesma forma, se eliminar a imagem do SKU subjacente, a imagem também seria removida do SKU privado.

<a name="restricting-the-audience"></a>Restringir o público
------------------------

As ofertas privadas só podem ser encontradas e implementadas por utilizadores direcionados.
Atualmente apoiamos utilizadores direcionados usando IDs de subscrição.

Estas subscrições podem ser inseridas através de um formulário de entrada manual **para até 10 subscrições,** ou através do upload de um ficheiro CSV, que permite **até 20.000 subscrições.**

Entrada Manual para público restrito:

![Restringir manualmente o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Upload CSV para audiência restrita:

![Use cSV para restringir o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Conteúdo de ficheiro CSV de amostra:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando muda de entrada manual para a vista de upload do CSV ou de CSV para entrada manual, a antiga lista de IDs de subscrição com acesso ao SKU não é mantida. Um aviso é apresentado e a lista só é substituída após a poupança da oferta.

<a name="managing-private-audiences"></a>Gerir audiências privadas
-------------------------

**Para atualizar o público sem reeditar a oferta, faz as alterações de audiência que pretende (utilizando a UI ou a API) e, em seguida, iniciar a ação "Sync Private Audiences".**

Se o seu público tiver 10 ou menos subscrições, pode geri-lo inteiramente utilizando o CPP UI.

Se o seu público tiver mais de 10 subscrições, pode geri-lo utilizando um ficheiro CSV que pode fazer o upload para o CPP UI ou utilizar a API.

Se estiver a utilizar a API e não quiser manter um ficheiro CSV, pode gerir o público diretamente utilizando a API de acordo com as instruções abaixo.

> [!NOTE]
> Utilize o ID de assinatura Azure (Planos e SKUs) ou iD do inquilino (apenas planos) para adicionar uma audiência à sua oferta privada.

###  <a name="managing-subscriptions-with-the-api"></a>Gestão de assinaturas com a API

Pode utilizar a API para carregar um CSV ou gerir o seu público diretamente (sem utilizar um CSV). Em geral, basta recuperar a sua oferta, atualizar o `restrictedAudience` objeto e, em seguida, submeter essas alterações de volta à sua oferta de forma a adicionar ou remover membros do público.

Eis como atualizar programáticamente a sua lista de audiências:

1. [Recupere os](../cloud-partner-portal-api-retrieve-specific-offer.md) dados da sua oferta:

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Encontre objetos de audiência restritos em cada SKU da oferta utilizando esta consulta JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Atualize os objetos de audiência restritos para a sua oferta.

    **Se originalmente fez o upload da lista de subscrições para a sua oferta privada a partir do ficheiro CSV:**

    Os seus *objetos restritos* de auditoria serão assim.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Para cada objeto de audiência restrito:

    a. Descarregue o conteúdo de `restrictedAudience.uploadedCsvUri` . O conteúdo é simplesmente um ficheiro CSV com cabeçalhos. Por exemplo:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Adicione ou elimine as subscrições no ficheiro CSV descarregado, conforme necessário.

    c. Faça o upload do ficheiro CSV atualizado para uma localização, como [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-overview.md) ou [o OneDrive,](https://onedrive.live.com)e crie um link apenas de leitura para o seu ficheiro. Este será o seu novo *SasUrl.*

    d. Atualize a `restrictedAudience.uploadedCsvUri` chave com o seu novo *SasUrl*.

    **Se inseriu manualmente a lista original de subscrições para a sua oferta privada a partir do Portal do Parceiro cloud:**

    Os seus *objetos restritos deudiência* serão parecidos com isto:

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

    a. Para cada objeto de audiência restrito, adicione ou elimine as entradas na `restrictedAudience.manualEntries` lista conforme necessário.

4. Quando terminar de atualizar todos os objetos *deudiência restritos* para cada SKU da sua oferta privada, [atualize a oferta:](../cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Com isso, a sua lista de audiências atualizada está agora em vigor.

<a name="previewing-private-offers"></a>Pré-visualização de ofertas privadas
-------------------------

Durante a fase de pré-visualização/encenação, apenas as subscrições de pré-visualização do nível de oferta poderão aceder ao SKU. Nesta fase de testes pode pré-visualizar a oferta como parece aos seus clientes-alvo.

Oferta De Pré-visualização de nível para aceder a ofertas encenadas:

![Ids de subscrição de pré-visualização](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Após a oferta estar ao vivo, apenas as subscrições restritas de audiência (inseridas através de entrada manual ou CSV) poderão visualizar e implementar o SKU privado. Recomendamos que **inclua sempre as suas próprias subscrições no público restrito** para o SKU privado para fins de validação.

>[!NOTE]
>Para fins de depuragem, as equipas de suporte e engenharia da Microsoft também terão acesso a estas ofertas privadas.
