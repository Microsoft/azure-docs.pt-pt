---
title: SKUs privados e planos Mercado Azure
description: Como utilizar sKUs privados para gerir a disponibilidade da oferta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280393"
---
<a name="private-skus-and-plans"></a>SKUs e planos privados
============

As SKUs privadas permitem-lhe restringir a disponibilidade de SKUs a clientes específicos. Quando um SKU é marcado como privado, não está disponível em nenhum catálogo público, incluindo no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal Azure.](https://portal.azure.com) No portal Azure, apenas os clientes com acesso ao SKU podem vê-lo. Além disso, seria também solicitado que tivessem acesso a ofertas privadas.

>[!NOTE]
>As SKUs privadas devem ter novos Ids SKU/Plan exclusivos para evitar qualquer conflito com as suas SKUs públicas.

Pode utilizar SKUs privados para lidar com os seguintes cenários:

1.  Publique software que deseja apenas disponível publicamente para clientes específicos e não disponível publicamente.
2.  Publique variações de software público a um preço personalizado para clientes específicos.
3.  Publique variações de software público com uma descrição e termos personalizados (através de nova oferta).

Se quiser apenas alterar o preço, pode reutilizar os discos de outro SKU na mesma oferta. Com SKUs privados, não é preciso reenviar discos através de SKUs.

<a name="mark-a-sku-private"></a>Marque um Privado SKU
---------------------

Para marcar um SKU como privado, altere a opção perguntando se o SKU é privado:

![Marque um SKU como privado](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Pode reutilizar os discos noutro SKU e modificar os preços ou a descrição. Para reutilizar os discos, selecione **Sim** como resposta ao pedido "Será que este SKU reutiliza imagens de um SKU público".

Se o SKU estiver marcado como privado e a oferta tiver outros SKUs com discos reutilizáveis, é necessário indicar que o SKU reutiliza discos de outro SKU. Também é obrigado a especificar o público-alvo para o SKU privado.

>[!NOTE]
>Depois de publicado, um SKU público não pode ser tornado privado.

<a name="select-an-image"></a>Selecione uma imagem
------------------

Pode fornecer novos discos para o SKU privado ou reutilizar os mesmos discos já fornecidos noutro SKU, apenas modificando os preços ou descrição. Para reutilizar os discos, selecione **Sim** como resposta à imagem "Será que este SKU reutiliza a imagem de um SKU público".

![Indicar reutilização da imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Depois de confirmar que o SKU reutiliza imagens, selecione a fonte *ou* base SKU para as imagens:

![Selecione uma imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Ao publicar a oferta, as imagens do SKU selecionado serão disponibilizadas sob o ID SKU privado com as tarifas/termos personalizados. O SKU privado só seria visível para o público-alvo.

Para atualizações de imagens, só seria necessário atualizar a imagem subjacente ao SKU. Nos bastidores, a imagem para o SKU privado também será atualizada automaticamente. Da mesma forma, se eliminar a imagem do SKU subjacente, a imagem também será removida do SKU privado.

<a name="restricting-the-audience"></a>Restringindo o público
------------------------

As ofertas privadas só podem ser encontradas e implementadas por utilizadores direcionados.
Atualmente apoiamos utilizadores direcionados usando ids de subscrição.

Estas subscrições podem ser inseridas através de um formulário de entrada manual **para até 10 assinaturas,** ou através do upload de um ficheiro CSV, que permite **até 20.000 subscrições**.

Entrada manual para público restrito:

![Restringir manualmente o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Upload CSV para audiência restrita:

![Use cSV para restringir o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Conteúdo de ficheiro CSV da amostra:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando muda da entrada manual para a vista de upload do CSV ou do CSV para a entrada manual, a antiga lista de Ids de subscrição com acesso ao SKU não é retida. É apresentado um aviso e a lista só é substituída ao salvar a oferta.

<a name="managing-private-audiences"></a>Gestão de audiências privadas
-------------------------

**Para atualizar o público sem reeditar toda a oferta, faça as alterações de audiência que deseja (usando a UI ou a API) e, em seguida, inicie a ação "Sync Private Audiences".**

Se o seu público tiver 10 ou menos subscrições, pode geri-lo inteiramente utilizando o CPP UI.

Se o seu público tiver mais de 10 subscrições, pode geri-lo utilizando um ficheiro CSV que pode fazer o upload para o CPP UI ou utilizar a API.

Se estiver a utilizar a API e não quiser manter um ficheiro CSV, pode gerir o público diretamente utilizando API de acordo com as instruções abaixo.

> [!NOTE]
> Utilize o ID de subscrição Azure (Planos e SKUs) ou ID do Inquilino (apenas planos) para adicionar uma audiência à sua oferta privada.

###  <a name="managing-subscriptions-with-the-api"></a>Gestão de subscrições com a API

Pode utilizar a API para carregar um CSV ou gerir o seu público diretamente (sem utilizar um CSV). Em geral, basta recuperar a sua `restrictedAudience` oferta, atualizar o objeto e, em seguida, submeter essas alterações à sua oferta de forma a adicionar ou remover os membros do público.

Eis como atualizar programáticamente a sua lista de audiências:

1. [Recupere os seus](cloud-partner-portal-api-retrieve-specific-offer.md) dados de oferta:

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Encontre objetos de audiência restritos em cada SKU da oferta usando esta consulta JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Atualize o objeto de audiência restrito para a sua oferta.

    **Se inicialmente fez o upload da lista de subscrição para a sua oferta privada a partir do ficheiro CSV:**

    O seu objeto de *audiência restrito* será assim.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Para cada objeto restrito do público:

    a. Descarregue `restrictedAudience.uploadedCsvUri`o conteúdo de . O conteúdo é simplesmente um ficheiro CSV com cabeçalhos. Por exemplo:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Adicione ou elimine subscrições no ficheiro CSV descarregado, conforme necessário.

    c. Faça o upload do ficheiro CSV atualizado para um local, como [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-overview.md) ou [o OneDrive,](https://onedrive.live.com)e crie um link apenas para leitura para o seu ficheiro. Este será o seu novo *SasUrl.*

    d. Atualize `restrictedAudience.uploadedCsvUri` a tecla com o seu novo *SasUrl*.

    **Se inseriu manualmente a lista original de subscrições para a sua oferta privada a partir do Portal do Parceiro cloud:**

    O seu objeto de *audiência restrito* será algo parecido com isto:

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

    a. Para cada objeto de audiência restrito, `restrictedAudience.manualEntries` adicione ou elimine as entradas na lista conforme necessário.

4. Quando terminar de atualizar todos os objetos *restritos do Audience* para cada SKU da sua oferta privada, [atualize a oferta:](cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Com isso, a sua lista atualizada de audiências está agora em vigor.

<a name="previewing-private-offers"></a>Pré-visualização de ofertas privadas
-------------------------

Durante a etapa de pré-visualização/encenação, apenas as subscrições de pré-visualização do nível de oferta poderão aceder ao SKU. Nesta fase de testes pode pré-visualizar a oferta tal como parece aos seus clientes-alvo.

Oferta De Pré-visualização de nível de subscrição para aceder a ofertas encenadas:

![Ids de subscrição de pré-visualização](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Após a oferta estar ao vivo, apenas as subscrições restritas de audiência (introduzidas por entrada manual ou CSV) poderão visualizar e implementar o SKU privado. Recomendamos que **inclua sempre as suas próprias subscrições no público restrito** para o SKU privado para fins de validação.

>[!NOTE]
>Para fins de depuração, as equipas de suporte e engenharia da Microsoft também terão acesso a estas ofertas privadas.
