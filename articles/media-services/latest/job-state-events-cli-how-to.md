---
title: Monitorar eventos dos serviços de mídia do Azure com a grade de eventos usando a CLI | Microsoft Docs
description: Este artigo mostra como assinar a grade de eventos para monitorar os eventos dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937290"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Criar e monitorar eventos de serviços de mídia com a grade de eventos usando o CLI do Azure

O Azure Event Grid é um serviço de eventos para a cloud. Esse serviço usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Os eventos dos serviços de mídia contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento dos serviços de mídia porque a propriedade eventType começa com "Microsoft. Media.". Para obter mais informações, consulte [esquemas de eventos dos serviços de mídia](media-services-event-schemas.md).

Neste artigo, você usa o CLI do Azure para assinar eventos para sua conta dos serviços de mídia do Azure. Em seguida, você dispara eventos para exibir o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. Neste artigo, você envia os eventos para um aplicativo Web que coleta e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Instalar e utilizar a CLI localmente, este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos [dos serviços de multimédia v3 CLI](https://aka.ms/ams-v3-cli-ref) comandos trabalham no Azure Cloud Shell. Recomenda-se para utilizar a CLI localmente.

- [Criar uma conta de Media Services](create-account-cli-how-to.md).

    Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de assinar os eventos para a conta dos serviços de mídia, vamos criar o ponto de extremidade para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Neste artigo, você implanta um [aplicativo Web predefinido](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Se você alternar para o site "Visualizador de grade de eventos do Azure", verá que ele ainda não tem eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Definir a subscrição do Azure

No comando seguinte, forneça o ID da subscrição do Azure que quer utilizar na conta dos Serviços de Multimédia. Pode ver uma lista de subscrições a que tem acesso ao navegar até [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Assinar eventos de serviços de mídia

Você assina um artigo para informar à grade de eventos quais eventos você deseja rastrear. O exemplo a seguir assina a conta dos serviços de mídia que você criou e passa a URL do site que você criou como o ponto de extremidade para notificação de eventos. 

Substitua `<event_subscription_name>` por um nome exclusivo para sua assinatura de evento. Para `<resource_group_name>` e `<ams_account_name>`, use os valores que você usou ao criar a conta dos serviços de mídia. Para o `<endpoint_URL>`, forneça a URL do seu aplicativo Web e adicione `api/updates` à URL do home page. Ao especificar o ponto de extremidade ao assinar, a grade de eventos manipula o roteamento de eventos para esse ponto de extremidade. 

1. Obter a ID do recurso

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Por exemplo:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Assinar os eventos

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Por exemplo:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Você pode obter um aviso de handshake de validação. Aguarde alguns minutos e o handshake deve ser validado.

Agora, vamos disparar eventos para ver como a grade de eventos distribui a mensagem para o ponto de extremidade.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Você pode disparar eventos para a conta dos serviços de mídia executando um trabalho de codificação. Você pode seguir [este guia de início rápido](stream-files-dotnet-quickstart.md) para codificar um arquivo e começar a enviar eventos. 

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. O ponto de extremidade precisa definir `validationResponse` como `validationCode`. Para obter mais informações, consulte [Event Grid segurança e autenticação](../../event-grid/security-authentication.md). Você pode exibir o código do aplicativo Web para ver como ele valida a assinatura.

> [!TIP]
> Selecione o ícone do olho para expandir os dados do evento. Não atualize a página, se você quiser exibir todos os eventos.

![Ver evento da subscrição](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passos seguintes

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)

