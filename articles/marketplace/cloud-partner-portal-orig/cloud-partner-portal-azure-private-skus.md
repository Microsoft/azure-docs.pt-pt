---
title: SKUs de privada e planos | Documentos da Microsoft
description: Como utilizar os SKUs privadas para gerir a disponibilidade da oferta.
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
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: dcc8b9fef75ba13b607d7fdda5bd55cc710165b9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837019"
---
<a name="private-skus-and-plans"></a>SKUs de privada e planos
============

SKUs de privada permitem-lhe restringir a disponibilidade de SKUs para clientes específicos. Quando um SKU é marcada como particular, não está disponível em qualquer catálogo público, incluindo no [do Azure Marketplace](https://azuremarketplace.microsoft.com) e o [portal do Azure](https://portal.azure.com). No portal do Azure, apenas os clientes com acesso para o SKU podem vê-lo. Além disso, eles poderia também ser pedidos que têm acesso a ofertas privadas.

>[!NOTE]
>SKUs de privada tem de ter novos Ids SKU/plano exclusivos para evitar qualquer conflito com os SKUs públicos.

Pode utilizar os SKUs privados para lidar com os seguintes cenários:

1.  Publica o software que pretende que apenas disponíveis publicamente para clientes específicos e publicamente disponíveis.
2.  Publica variações de software público a um preço personalizado para clientes específicos.
3.  Publica variações de software pública com uma descrição personalizada e termos (por meio da nova oferta).

Se pretender apenas alterar o preço, é possível reutilizar os discos a partir de outro SKU na oferta do mesmo. Com SKUs privadas, não tem de submeter novamente discos entre SKUs.

<a name="mark-a-sku-private"></a>Marcar um SKU privada
---------------------

Para marcar um SKU como particulares, alterne a opção de perguntar se o SKU é privado:

![Marcar um SKU como privadas](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Pode reutilizar os discos em outro SKU e modificar os preços ou a descrição. Para reutilizar os discos, selecione **Sim** como uma resposta a "Faz este SKU novamente utilizar imagens de um SKU pública" linha de comandos.

Se o SKU está marcado como privada e a oferta tem outros SKUs com discos reutilizáveis, é necessário para indicar que o SKU reutiliza discos a partir de outro SKU. Também é necessário especificar o público-alvo para o SKU de privados.

>[!NOTE]
>Depois de ser publicado, um SKU público não é possível efetuar privado.

<a name="select-an-image"></a>Selecione uma imagem
------------------

Pode fornecer novos discos para o SKU privado ou reutilizar os mesmos discos já fornecidos no outro SKU, apenas modificar os preços ou descrição. Para reutilizar os discos, selecione **Sim** como uma resposta ao pedido "Faz esta imagem de reutilização de SKU de um SKU pública".

![Indicar a reutilização de imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Depois de confirmar que o SKU reutiliza imagens a partir de outro SKU, que identifica o SKU que é a origem das imagens.

As instruções no ecrã seguinte captura de mostrar como identificar o SKU privado poderia reutilizar as imagens de SKU selecionada:

![Selecione uma imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Quando publica a oferta, as imagens de SKU selecionada são disponibilizadas sob o ID do SKU privada com os termos/taxas personalizados. O SKU privado apenas ficavam visível para a audiência segmentada.

Para atualizações de imagem, só precisaria atualizar imagem do SKU subjacente. Em segundo plano, na imagem para o SKU privada também será atualizada automaticamente. Da mesma forma, se eliminar a imagem do SKU subjacente, a imagem também será removida do SKU privada.

<a name="restricting-the-audience"></a>Restringir o público-alvo
------------------------

Ofertas privadas podem ser localizadas e implementadas apenas pelos utilizadores visados.
Atualmente suportamos visar utilizadores com Ids de subscrição.

Estas subscrições podem ser inseridas por meio de um formulário de entrada manual **para até 10 subscrições**, ou ao carregar um ficheiro CSV, que permite **para subscrições de até 20 000**.

Introdução manual para o público-alvo restrito:

![Restringir manualmente o público-alvo](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV a carregar para o público-alvo restrito:

![Utilizar CSV para restringir o público-alvo](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Conteúdo do ficheiro CSV de exemplo:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Ao alternar da introdução manual para CSV carregar a vista ou de CSV para introdução manual, a lista antiga de Ids de subscrição com o acesso para o SKU não é mantida. É apresentado um aviso e a lista apenas será substituída ao guardar a oferta.

<a name="sync-private-subscriptions"></a>Subscrições de sincronização privada
-------------------------

Quando adiciona subscrições a uma oferta publicada com uma SKU privada ou um plano, não é necessário voltar a publicar a oferta para adicionar informações de público-alvo. Basta use um ID de subscrição do Azure (planos e SKUs) ou o ID de inquilino (apenas de planos) para adicionar o público-alvo.

<a name="previewing-private-offers"></a>Pré-visualização privada oferece
-------------------------

Durante a pré-visualização/teste passo, apenas as subscrições de nível de pré-visualização de oferta poderão acessar o SKU. Esta é a fase de teste, nesse momento, pode validar que a oferta ficaria para seus clientes de destinados e é o padrão para todos os tipos de publicação.

Oferece subscrições de pré-visualização de nível para aceder a ofertas em etapas:

![Ids de subscrição de pré-visualização](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Depois da oferta está em direto, apenas as subscrições de público restrito (introduzidas por meio da introdução manual ou CSV) será capazes de ver e implementar o SKU privado. Recomendamos que **incluir sempre o seu próprio subscrições no público-alvo restrito** para o SKU privado para fins de validação.

>[!NOTE]
>Para fins de depuração, o suporte da Microsoft e equipas de engenharia também terão acesso a estas ofertas privadas.
