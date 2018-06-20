---
title: Enviar eventos personalizados do Azure Event Grid para o Hubs de Eventos | Microsoft Docs
description: Utilize o Azure Event Grid e a CLI do Azure para publicar um tópico e subscrever esse evento. É utilizado um hub de eventos para o ponto final.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 06/06/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 4233884d02377f54fa7c12b4a4a016c283f66a8d
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851018"
---
# <a name="route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>Encaminhar eventos personalizados para o Hubs de Eventos do Azure com a CLI do Azure e o Event Grid

O Azure Event Grid é um serviço de eventos para a cloud. Os Hubs de Eventos do Azure é um dos processadores de eventos suportados. Neste artigo, a CLI do Azure é utilizada para criar um tópico personalizado, subscrever o tópico e acionar o evento para ver o resultado. Envia eventos para um hub de eventos.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). 

O exemplo seguinte cria um grupo de recursos com o nome *gridResourceGroup* na localização *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Criar um Tópico Personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. O exemplo seguinte cria o tópico personalizado no seu grupo de recursos. Substitua `<your-topic-name>` por um nome exclusivo para o seu tópico. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS.

```azurecli-interactive
topicname=<your-topic-name>
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>Criar hub de eventos

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Cria um hub de eventos para a recolha de eventos.

```azurecli-interactive
namespace=<unique-namespace-name>
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>Subscrever um tópico

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar. O exemplo seguinte subscreve o tópico que criou e transmite o ID do recurso do hub de eventos para o ponto final. O ponto final está no formato:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

O script seguinte obtém o ID do recurso do hub de eventos e subscreve um tópico da grelha de eventos. Define o tipo de ponto final como `eventhub` e utiliza o ID do hub de eventos para o ponto final.

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --topic-name $topicname \
  -g gridResourceGroup \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o seu tópico

Vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Em primeiro lugar, vamos obter o URL e a chave do tópico personalizado.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artigo, vai utilizar dados do evento de exemplo para enviar para o tópico. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento. CURL é um utilitário que envia os pedidos HTTP. Neste artigo, utilize o CURL para enviar o evento para o tópico.  O exemplo seguinte envia três eventos para o tópico do Event Grid:

```azurecli-interactive
for i in 1 2 3
do
   body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
   curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
done
```

Navegue para o hub de eventos no portal e repare que o Event Grid enviou esses três eventos para o hub de eventos.

![Mostrar mensagens](./media/custom-event-to-eventhub/show-result.png)

Normalmente, cria uma aplicação que obtém os eventos do hub de eventos. Para criar uma aplicação que obtém mensagens de um hub de eventos, veja:

* [Começar a receber mensagens com o Anfitrião do Processador de Eventos em .NET Standard](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Receber eventos de Hubs de Eventos do Azure com o Java](../event-hubs/event-hubs-java-get-started-receive-eph.md)
* [Receber eventos dos Hubs de Eventos com o Apache Storm](../event-hubs/event-hubs-storm-getstarted-receive.md)

## <a name="clean-up-resources"></a>Limpar recursos
Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, utilize o comando seguinte para eliminar os recursos que criou neste artigo.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
