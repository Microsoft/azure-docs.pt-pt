---
title: Monitorar eventos dos serviços de mídia do Azure com a grade de eventos com o portal
description: Este artigo mostra como assinar a grade de eventos para monitorar os eventos dos serviços de mídia do Azure.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509228"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Criar e monitorar eventos de serviços de mídia com a grade de eventos usando o portal do Azure

O Azure Event Grid é um serviço de eventos para a cloud. Esse serviço usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Os eventos dos serviços de mídia contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento dos serviços de mídia porque a propriedade eventType começa com "Microsoft. Media.". Para obter mais informações, consulte [esquemas de eventos dos serviços de mídia](media-services-event-schemas.md).

Neste artigo, você usa o portal do Azure para assinar eventos para sua conta dos serviços de mídia do Azure. Em seguida, você dispara eventos para exibir o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No artigo, enviamos eventos para um aplicativo Web que coleta e exibe as mensagens.

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

## <a name="prerequisites"></a>Pré-requisitos 

* Ter uma assinatura ativa do Azure.
* Crie uma nova conta dos Serviços de Multimédia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de assinar os eventos para a conta dos serviços de mídia, vamos criar o ponto de extremidade para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Neste artigo, você implanta um [aplicativo Web predefinido](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Se você alternar para o site "Visualizador de grade de eventos do Azure", verá que ele ainda não tem eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Assinar eventos de serviços de mídia

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. No portal, selecione sua conta dos serviços de mídia e selecione **eventos**.
1. Para enviar eventos para a sua aplicação de visualizador, utilize um webhook para o ponto final. 

   ![Selecionar webhook](./media/monitor-events-portal/select-web-hook.png)

1. A assinatura de evento é preenchida com valores para sua conta de serviços de mídia. 
1. Selecione ' gancho da Web ' para o **tipo de ponto de extremidade**.
1. Neste tópico, deixamos a opção **inscrever-se em todos os tipos de eventos** verificados. No entanto, você pode desmarcar e filtrar por tipos de eventos específicos. 
1. Clique no link **selecionar um ponto de extremidade** .

    Para o ponto final do webhook, indique o URL da sua aplicação Web e adicione `api/updates` ao URL da home page. 

1. Pressione **confirmar seleção**.
1. Prima **Criar**.
1. Nomeie a subscrição.

   ![Selecionar registos](./media/monitor-events-portal/create-subscription.png)

1. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. 

    O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. O ponto de extremidade precisa definir `validationResponse` como `validationCode`. Para obter mais informações, consulte [Event Grid segurança e autenticação](../../event-grid/security-authentication.md). Você pode exibir o código do aplicativo Web para ver como ele valida a assinatura.

Agora, vamos disparar eventos para ver como a grade de eventos distribui a mensagem para o ponto de extremidade.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Você pode disparar eventos para a conta dos serviços de mídia executando um trabalho de codificação. Você pode seguir [este guia de início rápido](stream-files-dotnet-quickstart.md) para codificar um arquivo e começar a enviar eventos. Se você se inscreveu em todos os eventos, verá uma tela semelhante à seguinte:

> [!TIP]
> Selecione o ícone do olho para expandir os dados do evento. Não atualize a página, se você quiser exibir todos os eventos.

![Ver evento da subscrição](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passos seguintes

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)
