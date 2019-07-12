---
title: Como enviar eventos de serviço Azure SignalR para o Event Grid
description: Um guia para mostrar a como ativar eventos do Event Grid para o seu serviço SignalR, em seguida, Enviar ligação de cliente ligado/desligado eventos para um aplicativo de exemplo.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789179"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Como enviar eventos de serviço Azure SignalR para o Event Grid

O Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que fornece o consumo de eventos uniforme com um modelo de publicação. Neste guia, vai utilizar a CLI do Azure para criar um serviço Azure SignalR, assinar eventos de ligação, em seguida, implementar uma aplicação web de exemplo para receber os eventos. Por fim, pode ligar e desligar e ver o payload do evento no aplicativo de exemplo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos da CLI do Azure neste artigo são formatados para o **Bash** shell. Se estiver a utilizar um shell diferente, como o PowerShell ou da linha de comandos, terá de ajustar os caracteres de continuação de linha ou linhas de atribuição de variáveis em conformidade. Este artigo utiliza as variáveis para minimizar a quantidade de comando de edição necessárias.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual implementar e gerir recursos do Azure. O seguinte procedimento [criar grupo az][az-group-create] comando cria um grupo de recursos com o nome *myResourceGroup* no *eastus* região. Se pretender utilizar um nome diferente para o grupo de recursos, defina `RESOURCE_GROUP_NAME` para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Criar um Serviço SignalR

Em seguida, implemente um serviço Azure Signalr para o grupo de recursos com os seguintes comandos.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Quando o serviço SignalR tiver sido criado, a CLI do Azure devolve resultados semelhantes ao seguinte:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Criar um ponto de extremidade do evento

Nesta secção, vai utilizar um modelo do Resource Manager, localizado num repositório do GitHub para implementar uma aplicação de web de exemplo precompilados no App Service do Azure. Mais tarde, subscrever a eventos do Event Grid do seu registo e especifique esta aplicação como o ponto final ao qual os eventos são enviados.

Para implementar a aplicação de exemplo, defina `SITE_NAME` para um nome exclusivo para a sua aplicação web e execute os seguintes comandos. O nome do site tem de ser exclusivo no Azure porque ele faz parte do nome de domínio completamente qualificado (FQDN) da aplicação web. Numa seção posterior, navegue para o FQDN da aplicação num browser para ver eventos do seu registo.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Assim que a implementação com êxito (pode demorar alguns minutos), abra um browser e navegue para a aplicação web para garantir que ela está em execução:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subscrever eventos de registo

Na grelha de eventos, se inscreve para um *tópico* para dizer a ele os eventos que pretende controlar e onde enviá-los. O seguinte procedimento [criar subscrição de evento do eventgrid az][az-eventgrid-event-subscription-create] comando subscreve o serviço Azure SignalR que criou e especifica o URL da sua aplicação web como o ponto final para o qual ele deve enviar eventos. As variáveis de ambiente preenchido nas secções anteriores são reutilizadas aqui, portanto, não edições são necessárias.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Quando a subscrição estiver concluída, deve ver um resultado semelhante ao seguinte:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Eventos de registo do acionador

Mude para o modo de serviço para `Serverless Mode` e configurar uma ligação de cliente para o serviço de SignalR. Pode tirar [exemplo sem servidor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) como referência.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Ver eventos de registo

Agora ligou um cliente para o serviço de SignalR. Navegue até à sua aplicação web do Visualizador de grelha de eventos e deverá ver um `ClientConnectionConnected` eventos. Se encerrar o cliente, também poderá ver um `ClientConnectionDisconnected` eventos.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
