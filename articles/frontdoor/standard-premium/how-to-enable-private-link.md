---
title: Ligue o Azure Front Door Premium à sua origem com Link Privado
description: Saiba como ligar o seu Azure Front Door Premium à sua origem com o serviço Private Link utilizando o portal Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099459"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Ligue o Azure Front Door Premium à sua origem com Link Privado

Este artigo irá guiá-lo através da configuração do Azure Front Door Premium SKU para ligar às suas aplicações hospedadas numa rede virtual utilizando o serviço Azure Private Link.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Crie um serviço [de Ligação Privada](../../private-link/create-private-link-service-portal.md) para os seus servidores web de origem.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Ativar o ponto final privado no serviço Azure Front Door

Nesta secção, você irá mapear o serviço Azure Private Link para um ponto final privado criado na rede privada do Azure Front Door Premium SKU. 

1. Dentro do seu perfil Azure Front Door Premium, em *Definições*, selecione **Origin groups**.

1. Selecione o grupo de origem que contém a origem para a origem deseja ativar o Link Privado.

1. **Selecione + Adicione uma origem** para adicionar uma nova origem ou selecione uma origem previamente criada a partir da lista. Em seguida, selecione a caixa de verificação para ativar o **serviço de ligação privada**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Screenshot de ativar o link privado na adicionar uma página de origem.":::

1. Para **Selecionar um recurso Azure,** selecione **No meu diretório**. Selecione ou introduza a seguinte definição para configurar o recurso que pretende que o Azure Front Door Premium se conecte com o privado.
    
    | Definição | Valor |
    | ------- | ----- |
    | Region | Selecione a região que é igual ou mais próxima da sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Network/privateLinkServices**. |
    | Recurso | Selecione **myPrivateLinkService**. |
    | Sub-recurso-alvo | Deixe este campo vazio. |
    | Mensagem de pedido | Personalize a mensagem ou escolha a mensagem predefinida. |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [o Azure Front Door Premium Private Link](concept-private-link.md).
