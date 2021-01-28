---
title: Monitor Azure Media Services eventos com Grade de Eventos
description: Este artigo mostra como subscrever a Event Grid para monitorizar os eventos da Azure Media Services utilizando o Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9608dfd7ac4076e6cd74846c3f52ed2e3645777a
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956009"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Criar e monitorizar eventos de Serviços de Mídia com Grade de Eventos utilizando o Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Event Grid é um serviço de eventos para a cloud. Este serviço utiliza [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de eventos para assinantes. Os eventos dos Serviços de Comunicação Social contêm toda a informação necessária para responder às alterações nos seus dados. Pode identificar um evento de Media Services porque a propriedade eventType começa com "Microsoft.Media.". Para mais informações, consulte [os esquemas de eventos dos Media Services.](media-services-event-schemas.md)

Neste artigo, utiliza o CLI Azure para subscrever eventos para a sua conta Azure Media Services. Depois, disparas eventos para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. Neste artigo, envia os eventos para uma aplicação web que recolhe e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Instale e utilize o CLI localmente, este artigo requer a versão Azure CLI 2.0 ou posterior. Execute `az --version` para localizar a versão atual. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos os comandos [de Media Services v3 CLI](/cli/azure/ams) funcionam na Azure Cloud Shell. Recomenda-se a utilização do CLI localmente.

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

    Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever os eventos para a conta dos Serviços de Comunicação, vamos criar o ponto final para a mensagem do evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Neste artigo, implementa [uma aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Se mudar para o site "Azure Event Grid Viewer", verá que ainda não tem eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Definir a subscrição do Azure

No comando seguinte, forneça o ID da subscrição do Azure que quer utilizar na conta dos Serviços de Multimédia. Pode ver uma lista de subscrições a que tem acesso ao navegar até [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Subscreva eventos de Serviços de Comunicação Social

Subscreva um artigo para dizer à Grade de Eventos quais os eventos que pretende acompanhar. O exemplo a seguir subscreve a conta de Serviços de Comunicação que criou e passa o URL do website que criou como ponto final para notificação de eventos. 

`<event_subscription_name>`Substitua-o por um nome único para a subscrição do seu evento. Para `<resource_group_name>` `<ams_account_name>` e, utilize os valores utilizados na criação da conta Serviços de Comunicação. Para o `<endpoint_URL>` , forneça o URL da sua aplicação web e adicione ao URL da página `api/updates` inicial. Ao especificar o ponto final ao subscrever, a Grade de Eventos trata o encaminhamento dos eventos para esse ponto final. 

1. Obtenha o id de recursos

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Por exemplo:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Subscreva os eventos

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
    > Pode receber um aviso de aperto de mão de validação. Dê-lhe alguns minutos e o aperto de mão deve validar.

Agora, vamos desencadear eventos para ver como a Grade de Eventos distribui a mensagem para o seu ponto final.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Pode desencadear eventos para a conta dos Serviços de Comunicação Social executando um trabalho de codificação. Pode seguir [este quickstart](stream-files-dotnet-quickstart.md) para codificar um ficheiro e começar a enviar eventos. 

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. O ponto final tem que definir `validationResponse` para `validationCode` . Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](../../event-grid/security-authentication.md) Pode ver o código da aplicação web para ver como valida a subscrição.

> [!TIP]
> Selecione o ícone do olho para expandir os dados do evento. Não refresque a página, se quiser ver todos os eventos.

![Ver evento da subscrição](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passos seguintes

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)