---
title: Ligue o Azure Front Door Premium a uma origem interna do equilibrador de carga com o Link Privado
titleSuffix: Azure Private Link
description: Saiba como ligar o seu Azure Front Door Premium a um equilibrador interno de carga.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803822"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Ligue o Azure Front Door Premium a uma origem interna do equilibrador de carga com o Link Privado

Este artigo irá guiá-lo através da configuração do Azure Front Door Premium SKU para ligar à sua origem do balançador de carga interna utilizando o serviço Azure Private Link.

## <a name="prerequisites"></a>Pré-requisitos

Crie um [serviço de ligação privada.](../../private-link/create-private-link-service-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Ativar a Ligação Privada a um equilibrador de carga interno
 
Nesta secção, você irá mapear o serviço Private Link para um ponto final privado criado na rede privada da Azure Front Door. 

1. Dentro do seu perfil Azure Front Door Premium, em *Definições*, selecione **Origin groups**.

1. Selecione o grupo de origem que pretende ativar o Link Privado para o equilibrador de carga interno.

1. **Selecione + Adicione uma origem** para adicionar uma origem do balançador de carga interna.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Screenshot de permitir a ligação privada a um equilibrador de carga interno.":::

1. Para **Selecionar um recurso Azure,** selecione **No meu diretório**. Selecione ou introduza as seguintes definições para configurar o site que pretende que o Azure Front Door Premium se conecte com o privado.

    | Definição | Valor |
    | ------- | ----- |
    | Region | Selecione a região que é igual ou mais próxima da sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Network/privateLinkServices**. |
    | Recurso | Selecione o seu link Private ligado ao balançador de carga interno. |
    | Sub-recurso-alvo | Deixe em branco. |
    | Mensagem de pedido | Personalize a mensagem ou escolha o padrão. |

1. Em seguida, **selecione Adicionar** e, em seguida, **Atualizar** para guardar a sua configuração.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Aprovar a ligação privada do ponto final a partir da conta de armazenamento

1. Vá ao Private Link Center e selecione **serviços de ligação privada.** Em seguida, selecione o nome do seu link Privado.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Screenshot da lista de ligações privadas.":::

1. Selecione **ligações de ponto final privado em** *Definições*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Screenshot da página geral do link privado.":::

1. Selecione o pedido de ponto final privado *pendente* do Azure Front Door Premium e, em seguida, selecione **Aprovar**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Screenshot da aprovação pendente para ligação privada.":::

1. Uma vez aprovado, deve parecer a imagem abaixo. Levará alguns minutos para que a ligação se estabeleça completamente. Pode agora aceder ao seu equilibrador de carga interno a partir do Azure Front Door Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Screenshot do pedido de ligação privada aprovado.":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o serviço Private Link](../../private-link/private-link-service-overview.md).
