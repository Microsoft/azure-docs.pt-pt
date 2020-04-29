---
title: Como enviar eventos do Serviço De Sinalização Azure para a Grelha de Eventos
description: Um guia para lhe mostrar como ativar eventos da Grelha de Eventos para o seu Serviço SignalR, em seguida, enviar eventos de ligação ao cliente conectados/desligados para uma aplicação de amostra.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76710826"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Como enviar eventos do Azure SignalR Service ao Event Grid

Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que fornece um consumo uniforme de eventousando um modelo pub-sub. Neste guia, utiliza o Azure CLI para criar um Serviço de Sinalização Azure, subscrever eventos de ligação e, em seguida, implementar uma aplicação web de amostra para receber os eventos. Finalmente, pode ligar e desligar e ver a carga útil do evento na aplicação da amostra.

Se não tiver uma subscrição Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos Azure CLI neste artigo são formatados para **a** concha bash. Se estiver a utilizar uma concha diferente, como powerShell ou Command Prompt, poderá ter de ajustar caracteres de continuação da linha ou linhas de atribuição variáveis em conformidade. Este artigo utiliza variáveis para minimizar a quantidade de edição de comando necessária.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos Azure é um recipiente lógico no qual você implanta e gere os seus recursos Azure. O seguinte [grupo az criar][az-group-create] comando cria um grupo de recursos chamado *myResourceGroup* na região *oriental.* Se quiser usar um nome diferente para `RESOURCE_GROUP_NAME` o seu grupo de recursos, detetete tacada para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Criar um Serviço SignalR

Em seguida, insere um Serviço de Sinalizador Azure no grupo de recursos com os seguintes comandos.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Uma vez criado o Serviço SignalR, o Azure CLI devolve a saída semelhante à seguinte:

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

## <a name="create-an-event-endpoint"></a>Criar um ponto final de evento

Nesta secção, você usa um modelo de Gestor de Recursos localizado num repositório GitHub para implementar uma aplicação web de amostra pré-construída para o Serviço de Aplicações Azure. Mais tarde, subscreve os eventos da Grelha de Eventos do seu registo e especifique esta aplicação como o ponto final para o qual os eventos são enviados.

Para implementar a aplicação de amostra, detete `SITE_NAME` tede um nome único para a sua aplicação web e execute os seguintes comandos. O nome do site deve ser único dentro do Azure porque faz parte do nome de domínio totalmente qualificado (FQDN) da aplicação web. Numa secção posterior, navega para o FQDN da aplicação num navegador web para ver os eventos do seu registo.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Assim que a implementação for bem sucedida (pode demorar alguns minutos), abra um navegador e navegue para a sua aplicação web para se certificar de que está em execução:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subscreva eventos de registo

Em Event Grid, subscreve um *tópico* para lhe dizer quais os eventos que pretende acompanhar e para onde enviá-los. O seguinte [az eventgrid eventgrid cria][az-eventgrid-event-subscription-create] um comando subscrito satisfaça o Serviço De Sinalização Azure que criou, e especifica o URL da sua aplicação web como o ponto final para o qual deve enviar eventos. As variáveis ambientais que povoou em secções anteriores são reutilizadas aqui, por isso não são necessárias edições.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Quando a subscrição estiver concluída, deverá ver a saída semelhante à seguinte:

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

## <a name="trigger-registry-events"></a>Eventos de registo de gatilho

Mude para o `Serverless Mode` modo de serviço e instale uma ligação do cliente ao Serviço SignalR. Pode tomar a [Amostra Sem Servidor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) como referência.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Ver eventos de registo

Ligou um cliente ao Serviço SignalR. Navegue para a sua aplicação web `ClientConnectionConnected` Event Grid Viewer, e deverá ver um evento. Se rescindir o cliente, também `ClientConnectionDisconnected` verá um evento.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
