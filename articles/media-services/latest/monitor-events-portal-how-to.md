---
title: Monitorizar eventos de serviços de multimédia do Azure com o Event Grid através do portal | Documentos da Microsoft
description: Este artigo mostra como subscrever Event Grid para monitorar eventos dos serviços de multimédia do Azure.
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: d4592c93cb7969c45a107d7365a1b9dabf11f412
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884040"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Criar e monitorizar eventos de serviços de multimédia com o Event Grid com o portal do Azure

O Azure Event Grid é um serviço de eventos para a cloud. Este serviço utiliza [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Eventos de serviços de multimédia contêm todas as informações que necessárias para responder a alterações nos seus dados. É possível identificar um evento de serviços de multimédia, porque a propriedade eventType começa com "Microsoft.Media.". Para obter mais informações, consulte [esquemas de eventos dos serviços de multimédia](media-services-event-schemas.md).

Neste artigo, utilize o portal do Azure para subscrever eventos para a sua conta de Media Services do Azure. Em seguida, vai acionar eventos para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. Neste artigo, enviamos eventos para uma aplicação web que recolhe e apresenta as mensagens.

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

## <a name="prerequisites"></a>Pré-requisitos 

* Ter uma subscrição do Azure Active Directory.
* Crie uma nova conta dos Serviços de Multimédia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever os eventos para a conta de Media Services, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Neste artigo, vai implementar um [aplicação web pré-criados](https://github.com/Azure-Samples/azure-event-grid-viewer) que apresenta as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Se mudar para o site de "Visualizador de grelha de eventos do Azure", verá tem ainda não existem eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Subscrever eventos dos serviços de multimédia

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. No portal, selecione a sua conta de Media Services e selecione **eventos**.
1. Para enviar eventos para a sua aplicação de visualizador, utilize um webhook para o ponto final. 

   ![Selecionar webhook](./media/monitor-events-portal/select-web-hook.png)

1. A subscrição de evento é prefilled com valores para a sua conta de Media Services. 
1. Selecione o Hook de Web para o **tipo de ponto final**.
1. Neste tópico, vamos deixar o **subscrever todos os tipos de evento** marcada. No entanto, pode desmarcar ele e o filtro para tipos de eventos específicos. 
1. Clique nas **selecione um ponto de extremidade** ligação.

    Para o ponto final do webhook, indique o URL da sua aplicação Web e adicione `api/updates` ao URL da home page. 

1. Prima **confirmar seleção**.
1. Prima **Criar**.
1. Nomeie a subscrição.

   ![Selecionar registos](./media/monitor-events-portal/create-subscription.png)

1. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. 

    O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. O ponto final tem de definir `validationResponse` para `validationCode`. Para obter mais informações, consulte [Event Grid segurança e autenticação](../../event-grid/security-authentication.md). Pode ver o código da aplicação web para ver como ele valida a subscrição.

Agora, vamos acionar eventos para ver como o Event Grid distribui a mensagem para o ponto final.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Pode acionar eventos para a conta de Media Services ao executar uma tarefa de codificação. Pode seguir [este guia de introdução](stream-files-dotnet-quickstart.md) para codificar um ficheiro e iniciar o envio de eventos. Se assina todos os eventos, verá um ecrã semelhante ao seguinte:

> [!TIP]
> Selecione o ícone do olho para expandir os dados do evento. Não atualize a página, se desejar exibir todos os eventos.

![Ver evento da subscrição](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar e transmitir em fluxo](stream-files-tutorial-with-api.md)
