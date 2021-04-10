---
title: Quickstart - Porto um número de telefone nos Serviços de Comunicação Azure
description: Saiba como encaminhar um número de telefone para o seu recurso de Serviços de Comunicação
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729933"
---
# <a name="quickstart-port-a-phone-number"></a>Quickstart: Port um número de telefone

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Inicie-se com os Serviços de Comunicação Azure, colocando o seu número de telefone no seu recurso Azure Communication Services. Os números de portagens e geográficos baseados nos Estados Unidos são elegíveis para a porção. Para obter mais informações sobre os tipos de números de telefone, visite a documentação conceptual do [número de telefone.](../../concepts/telephony-sms/plan-solution.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Um recurso ativo dos Serviços de Comunicação.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Reúna os seus dados de recursos Azure

Antes de iniciar um pedido portuário, navegue até ao portal Azure e selecione o seu recurso de Serviços de Comunicação. Com o `Overview` painel selecionado, clique no `JSON View` link no canto superior direito:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Screenshot mostrando a seleção da vista JSON.":::

Grave o **ID** do seu recurso e **imutável ID de recursos:**

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Screenshot mostrando a seleção das propriedades JSON.":::

## <a name="initiate-the-port-request"></a>Iniciar o pedido portuário

Os números de portagens e geográficos baseados nos Estados Unidos são elegíveis para a porção. Utilize um dos seguintes formulários para submeter o seu pedido portuário:

- Para números gratuitos: [Pedido de porta de número gratuito](https://aka.ms/acs-port-form-tollfree)
- Para números geográficos baseados nos EUA: [Pedido de porta de número geográfico](https://aka.ms/acs-port-form-geographic)

Quando estiver concluído, envie o formulário de pedido de porta preenchido para acsporting@microsoft.com . Certifique-se de que a sua linha de e-mail começa com "ACS Port-In Request".

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido aprendeu a:

> [!div class="checklist"]
> * Adquirir os seus metadados de recursos de comunicação
> * Envie um pedido para portar o seu número de telefone

> [!div class="nextstepaction"]
> [Enviar um SMS](../telephony-sms/send.md) 
>  [Começa com a chamada](../voice-video-calling/getting-started-with-calling.md)
