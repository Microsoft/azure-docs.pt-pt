---
title: Monitor Azure Media Services eventos com Rede de Eventos com portal
description: Este artigo mostra como subscrever a Rede de Eventos para monitorizar os eventos da Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509228"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Create and monitor Media Services events with Event Grid using the Azure portal (Criar e monitorizar eventos de Serviços de Multimédia com o Event Grid com o portal do Azure)

O Azure Event Grid é um serviço de eventos para a cloud. Este serviço utiliza subscrições de [eventos](../../event-grid/concepts.md#event-subscriptions) para direcionar mensagens de eventos para assinantes. Os eventos dos Serviços de Media contêm todas as informações necessárias para responder a alterações nos seus dados. Pode identificar um evento de Media Services porque a propriedade eventType começa com "Microsoft.Media.". Para mais informações, consulte o [evento de Media Services.](media-services-event-schemas.md)

Neste artigo, utiliza o portal Azure para subscrever eventos para a sua conta Azure Media Services. Depois, desencadeas eventos para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No artigo, enviamos eventos para uma aplicação web que recolhe e exibe as mensagens.

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

## <a name="prerequisites"></a>Pré-requisitos 

* Tenha uma subscrição azure ativa.
* Crie uma nova conta dos Serviços de Multimédia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever os eventos para a conta media Services, vamos criar o ponto final para a mensagem do evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Neste artigo, você implementa uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Se mudar para o site "Azure Event Grid Viewer", verá que ainda não tem eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Subscreva eventos de Serviços de Media

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. No portal, selecione a sua conta Media Services e selecione **Eventos**.
1. Para enviar eventos para a sua aplicação de visualizador, utilize um webhook para o ponto final. 

   ![Selecionar webhook](./media/monitor-events-portal/select-web-hook.png)

1. A subscrição do evento está preenchida com valores para a sua conta Media Services. 
1. Selecione 'Web Hook' para o **tipo de ponto final**.
1. Neste tópico, deixamos o Subscrever todos os tipos de **eventos** verificados. No entanto, pode desmascará-lo e filtrar para tipos específicos de eventos. 
1. Clique no link **'Seleccionar'.**

    Para o ponto final do webhook, indique o URL da sua aplicação Web e adicione `api/updates` ao URL da home page. 

1. Prima **confirmar a seleção**.
1. Prima **Criar**.
1. Nomeie a subscrição.

   ![Selecionar registos](./media/monitor-events-portal/create-subscription.png)

1. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. 

    O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. O ponto final `validationResponse` tem `validationCode`que definir para . Para mais informações, consulte [a segurança e a autenticação da Rede de Eventos.](../../event-grid/security-authentication.md) Pode ver o código da aplicação web para ver como valida a subscrição.

Agora, vamos desencadear eventos para ver como a Grelha de Eventos distribui a mensagem para o seu ponto final.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Pode desencadear eventos para a conta de Media Services executando um trabalho de codificação. Pode seguir [este quickstart](stream-files-dotnet-quickstart.md) para codificar um ficheiro e começar a enviar eventos. Se subscreveu todos os eventos, verá um ecrã semelhante ao seguinte:

> [!TIP]
> Selecione o ícone do olho para expandir os dados do evento. Não refresque a página, se quiser ver todos os eventos.

![Ver evento da subscrição](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passos seguintes

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)
