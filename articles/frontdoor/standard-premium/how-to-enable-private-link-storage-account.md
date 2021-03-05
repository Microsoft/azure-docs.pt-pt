---
title: Ligue o Azure Front Door Premium a uma conta de armazenamento de origem com Link Privado
titleSuffix: Azure Private Link
description: Saiba como ligar o Seu Azure Front Door Premium a uma conta de armazenamento privada.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201673"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Ligue o Azure Front Door Premium a uma conta de armazenamento de origem com Link Privado

Este artigo irá guiá-lo através da configuração do Azure Front Door Premium SKU para ligar à sua conta de armazenamento origem privada usando o serviço Azure Private Link.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Ativar o Link Privado para uma conta de armazenamento
 
Nesta secção, você irá mapear o serviço Private Link para um ponto final privado criado na rede privada da Azure Front Door. 

1. Dentro do seu perfil Azure Front Door Premium, em *Definições*, selecione **Origin groups**.

1. Selecione o grupo de origem que contém a origem da conta de armazenamento para a origem deseja ativar o Link Privado.

1. **Selecione + Adicione uma origem** para adicionar uma nova origem da conta de armazenamento ou selecione uma origem de conta de armazenamento previamente criada a partir da lista.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Screenshot de permitir o link privado para uma conta de armazenamento.":::

1. Para **Selecionar um recurso Azure,** selecione **No meu diretório**. Selecione ou introduza as seguintes definições para configurar o site que pretende que o Azure Front Door Premium se conecte com o privado.

    | Definição | Valor |
    | ------- | ----- |
    | Region | Selecione a região que é igual ou mais próxima da sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Storage/storageAcounts**. |
    | Recurso | Selecione a sua conta de armazenamento. |
    | Sub-recurso-alvo | Pode selecionar *blob* ou *web.* |
    | Mensagem de pedido | Personalize a mensagem ou escolha o padrão. |

1. Em seguida, **selecione Adicionar** para guardar a sua configuração.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Aprovar a ligação privada do ponto final a partir da conta de armazenamento

1. Vá à conta de armazenamento que configura o Private Link na última secção. Selecione **rede em** **Definições**.

1. Em **Networking**, selecione **ligações de ponto final privados**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Screenshot das definições de rede numa Aplicação Web.":::

1. Selecione o pedido de ponto final privado *pendente* do Azure Front Door Premium e, em seguida, selecione **Aprovar**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Screenshot do pedido de ponto final privado de armazenamento pendente.":::

1. Uma vez aprovado, deve parecer a imagem abaixo. Levará alguns minutos para que a ligação se estabeleça completamente. Já pode aceder à sua conta de armazenamento a partir do Azure Front Door Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Screenshot do pedido de ponto final de armazenamento aprovado.":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o serviço Private Link com conta de armazenamento.](../../storage/common/storage-private-endpoints.md)
