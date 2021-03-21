---
title: Ligue o Azure Front Door Premium a uma origem de aplicações web com Link Privado
titleSuffix: Azure Private Link
description: Saiba como ligar o seu Azure Front Door Premium a um webapp em privado.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030584"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Ligue o Azure Front Door Premium a uma origem de aplicação web com link privado

Este artigo irá guiá-lo através da configuração do Azure Front Door Premium SKU para se conectar à sua Web App de forma privada utilizando o serviço Azure Private Link.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Crie um serviço [de Ligação Privada](../../private-link/create-private-link-service-portal.md) para os seus servidores web de origem.

> [!Note]
> O Private Endpoint está disponível em regiões públicas para aplicações web PremiumV2 de nível PremiumV3, aplicações web Do 43 ºC, aplicações web Linux e o plano Azure Functions Premium (por vezes referido como o plano Elastic Premium).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Ativar o Link Privado para uma Aplicação Web em Azure Front Door Premium
 
Nesta secção, você irá mapear o serviço Private Link para um ponto final privado criado na rede privada da Azure Front Door. 

1. Dentro do seu perfil Azure Front Door Premium, em *Definições*, selecione **Origin groups**.

1. Selecione o grupo de origem que contém a origem da Web App para a seguinte forma ativar o Link Privado.

1. **Selecione + Adicione uma origem** para adicionar uma nova origem de aplicações web ou selecione uma origem de aplicações web previamente criada a partir da lista.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Screenshot de permitir ligação privada a uma Web App.":::

1. Para **Selecionar um recurso Azure,** selecione **No meu diretório**. Selecione ou introduza as seguintes definições para configurar o site que pretende que o Azure Front Door Premium se conecte com o privado.

    | Definição | Valor |
    | ------- | ----- |
    | Region | Selecione a região que é igual ou mais próxima da sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Web/sites**. |
    | Recurso | Selecione **myPrivateLinkService**. |
    | Sub-recurso-alvo | sites |
    | Mensagem de pedido | Personalize a mensagem ou escolha o padrão. |

1. Em seguida, **selecione Adicionar** para guardar a sua configuração.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Aprovar a ligação de ponto final privado Azure Front Door Premium a partir da Web App

1. Vá à Web App que configura o Private Link na última secção. Selecione **rede em** **Definições**.

1. Em **Networking**, selecione **Configurar as suas ligações de ponto final privado**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Screenshot das definições de rede numa Aplicação Web.":::

1. Selecione o pedido de ponto final privado *pendente* do Azure Front Door Premium e, em seguida, selecione **Aprovar**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Screenshot do pedido de ponto final privado pendente.":::

1. Uma vez aprovado, deve parecer a imagem abaixo. Levará alguns minutos para que a ligação se estabeleça completamente. Já pode aceder à sua aplicação web a partir do Azure Front Door Premium. O acesso direto à Web App a partir da internet pública é desativado após o ponto final privado ser ativado.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Screenshot do pedido de ponto final aprovado.":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o serviço Private Link com a Azure Web App.](../../app-service/networking/private-endpoint.md)
