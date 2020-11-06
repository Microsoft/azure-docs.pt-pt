---
title: Tutorial - Debug APIs in Azure API Management usando rastreio de pedido
description: Siga os passos deste tutorial para permitir rastrear e inspecionar etapas de processamento de pedidos na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: e9a101de408b506fb5375b5f16c1deff4f67532d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422006"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Tutorial: Debug seus APIs usando rastreio de pedido

Este tutorial descreve como inspecionar (rastrear) o processamento de pedidos na Azure API Management para ajudá-lo a depurar e resolver problemas na sua API. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Traçar uma chamada de exemplo
> * Rever etapas de processamento de pedidos

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="Inspetor de API":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Complete o seguinte tutorial: [Importe e publique a sua primeira API.](import-and-publish.md)

## <a name="verify-allow-tracing-setting"></a>Verifique a definição de rastreio de permitir 

A **definição de rastreio de permitir** a subscrição utilizada para a sua API deve ser ativada. Se estiver a utilizar a subscrição incorporada de acesso total, está ativada por padrão. Para verificar no portal, navegue para a sua instância de Gestão de API e selecione **Subscrições.**

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Permitir rastreio para subscrição":::

## <a name="trace-a-call"></a>Rastrear uma chamada

1. Inscreva-se no [portal Azure](https://portal.azure.com)e navegue para a sua instância de Gestão da API.
1. Selecione **APIs**.
1. Selecione API de  **Conferência de Demonstração** da sua lista de API.
1. Selecione o separador **Teste**.
1. Selecione a operação **GetSpeakers**.
1. Confirme que o cabeçalho de pedido HTTP inclui **Ocp-Admin-Trace: Verdadeiro** e um valor válido para **o Ocp-Admin-Subscription-Key**. Se não for, selecione **+ Adicione o cabeçalho** para adicionar o cabeçalho.
1. Selecione **Enviar** para fazer uma chamada de API.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Configurar o rastreio da API":::

> [!TIP]
> Se **a chave de subscrição Ocp-Apim não** estiver automaticamente preenchida no pedido HTTP, poderá recuperá-la no portal. Selecione **Subscrições** e abra o menu de contexto **(...** ) para a sua suscriptação. Selecione **as teclas Show/Hide**. Também pode regenerar chaves, se necessário. Em seguida, adicione uma chave ao cabeçalho.

## <a name="review-trace-information"></a>Rever informações de vestígios

1. Depois de concluída a chamada, aceda ao **separador Trace** na **Resposta HTTP**.
1. Selecione qualquer uma das seguintes ligações para saltar para informações detalhadas de **rastreá-lo: Entrada,** **Backend,** **Outbound**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Rastreio de resposta de revisão":::

    * **Entrada** - Mostra o pedido original que a API Management recebeu do chamador e as políticas aplicadas ao pedido. Por exemplo, se adicionar políticas em [Tutorial: Transforme e proteja a sua API,](transform-api.md)elas aparecerão aqui.

    * **Backend** - Mostra os pedidos da API Management enviados para o backend da API e a resposta que recebeu.

    * **Saída** - Mostra as políticas aplicadas à resposta antes de enviar de volta para o chamador.

    > [!TIP]
    > Cada passo mostra também o tempo decorrido desde que o pedido é recebido através da Gestão de API.

1. No separador **Mensagem,** o cabeçalho **ocp-apim-trace-location** mostra a localização dos dados de vestígios armazenados no armazenamento de blob Azure. Se necessário, vá a este local para recuperar os vestígios.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Localização de vestígios no Armazenamento Azure":::
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Traçar uma chamada de exemplo
> * Rever etapas de processamento de pedidos

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Usar revisões](api-management-get-started-revise-api.md)
