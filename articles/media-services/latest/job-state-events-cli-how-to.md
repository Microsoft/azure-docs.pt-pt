---
title: Monitorizar eventos de serviços de multimédia do Azure com o Event Grid com a CLI | Documentos da Microsoft
description: Este artigo mostra como subscrever Event Grid para monitorar eventos dos serviços de multimédia do Azure.
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
ms.openlocfilehash: f6243bbc21466361aed7cbb7193f3a7b7c7e539f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885021"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Criar e monitorizar eventos de serviços de multimédia com o Event Grid com a CLI do Azure

O Azure Event Grid é um serviço de eventos para a cloud. Este serviço utiliza [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Eventos de serviços de multimédia contêm todas as informações que necessárias para responder a alterações nos seus dados. É possível identificar um evento de serviços de multimédia, porque a propriedade eventType começa com "Microsoft.Media.". Para obter mais informações, consulte [esquemas de eventos dos serviços de multimédia](media-services-event-schemas.md).

Neste artigo, de usar a CLI do Azure para subscrever eventos para a sua conta de Media Services do Azure. Em seguida, vai acionar eventos para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. Neste artigo, irá enviar eventos para uma aplicação web que recolhe e apresenta as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Instalar e utilizar a CLI localmente, este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos [dos serviços de multimédia v3 CLI](https://aka.ms/ams-v3-cli-ref) comandos trabalham no Azure Cloud Shell. Recomenda-se para utilizar a CLI localmente.

- [Criar uma conta de Media Services](create-account-cli-how-to.md).

    Lembre-se de que não se esqueça dos valores que utilizou para o nome do grupo de recursos e o nome de conta de serviços de multimédia.

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever os eventos para a conta de Media Services, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Neste artigo, vai implementar um [aplicação web pré-criados](https://github.com/Azure-Samples/azure-event-grid-viewer) que apresenta as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Se mudar para o site de "Visualizador de grelha de eventos do Azure", verá tem ainda não existem eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Definir a subscrição do Azure

No comando seguinte, forneça o ID da subscrição do Azure que quer utilizar na conta dos Serviços de Multimédia. Pode ver uma lista de subscrições a que tem acesso ao navegar até [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Subscrever eventos dos serviços de multimédia

Inscreva-se a um artigo para comunicar ao Event Grid os eventos que pretende controlar. O exemplo seguinte subscreve à conta de serviços de multimédia que criou e transmite o URL do Web site que criou como o ponto final para notificação de eventos. 

Substitua `<event_subscription_name>` com um nome exclusivo para a sua subscrição de evento. Para `<resource_group_name>` e `<ams_account_name>`, utilize os valores que utilizou quando criou a conta de Media Services. Para o `<endpoint_URL>`, forneça o URL da sua aplicação web e adicionar `api/updates` para o URL da home page. Especificando o ponto final quando subscrever, o Event Grid processa o encaminhamento de eventos para esse ponto final. 

1. Obter o id de recurso

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Por exemplo:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Subscrever os eventos

    ```azurecli
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Por exemplo:

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Poderá receber o aviso de handshake de validação. Atribua-lhe alguns minutos e deve validar o handshake.

Agora, vamos acionar eventos para ver como o Event Grid distribui a mensagem para o ponto final.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Pode acionar eventos para a conta de Media Services ao executar uma tarefa de codificação. Pode seguir [este guia de introdução](stream-files-dotnet-quickstart.md) para codificar um ficheiro e iniciar o envio de eventos. 

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. O ponto final tem de definir `validationResponse` para `validationCode`. Para obter mais informações, consulte [Event Grid segurança e autenticação](../../event-grid/security-authentication.md). Pode ver o código da aplicação web para ver como ele valida a subscrição.

> [!TIP]
> Selecione o ícone do olho para expandir os dados do evento. Não atualize a página, se desejar exibir todos os eventos.

![Ver evento da subscrição](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar e transmitir em fluxo](stream-files-tutorial-with-api.md)

