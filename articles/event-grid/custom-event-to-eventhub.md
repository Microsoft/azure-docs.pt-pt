---
title: 'Quickstart: Enviar eventos personalizados para Centros de Eventos - Grade de Eventos, Azure CLI'
description: 'Quickstart: Use Azure Event Grid e Azure CLI para publicar um tópico e subscrever esse evento. É utilizado um hub de eventos para o ponto final.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: e1989c94cc7b260cfc36a3129d2743350ca430bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780499"
---
# <a name="quickstart-route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>Quickstart: Encaminhe eventos personalizados para Azure Event Hubs com Azure CLI e Grade de Eventos

O Azure Event Grid é um serviço de eventos para a cloud. Os Hubs de Eventos do Azure é um dos processadores de eventos suportados. Neste artigo, a CLI do Azure serve para criar um tópico personalizado, subscrever o tópico personalizado e acionar o evento para ver o resultado. Envia eventos para um hub de eventos.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). 

O exemplo seguinte cria um grupo de recursos com o nome *gridResourceGroup* na localização *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Criar um Tópico Personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. O exemplo seguinte cria o tópico personalizado no seu grupo de recursos. Substitua `<your-topic-name>` por um nome exclusivo para o seu tópico personalizado. O nome do tópico personalizado deve ser exclusivo, porque este é representado por uma entrada DNS.

```azurecli-interactive
topicname=<your-topic-name>
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>Criar hub de eventos

Antes de subscrever o tópico personalizado, vamos criar o ponto final para a mensagem de evento. Cria um hub de eventos para a recolha de eventos.

```azurecli-interactive
namespace=<unique-namespace-name>
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Subscrever um tópico personalizado

Você subscreve um tópico de grelha de eventos para dizer a Event Grid quais eventos você quer rastrear. O exemplo a seguir subscreve o tópico personalizado que criou e passa o ID de recursos do centro de eventos para o ponto final. O ponto final está no formato:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

O script seguinte obtém o ID do recurso do hub de eventos e subscreve um tópico da grelha de eventos. Define o tipo de ponto final como `eventhub` e utiliza o ID do hub de eventos para o ponto final.

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)
topicid=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

A conta que cria a subscrição de evento deve ter acesso de escrita ao hub de eventos.

## <a name="send-an-event-to-your-custom-topic"></a>Enviar um evento para o tópico personalizado

Vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Em primeiro lugar, vamos obter o URL e a chave do tópico personalizado.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artigo, vai utilizar dados do evento de exemplo para enviar para o tópico personalizado. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento. CURL é um utilitário que envia os pedidos HTTP. Neste artigo, utilize o CURL para enviar o evento para o tópico personalizado.  O exemplo seguinte envia três eventos para o tópico do Event Grid:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Navegue para o hub de eventos no portal e repare que o Event Grid enviou esses três eventos para o hub de eventos.

![Mostrar mensagens](./media/custom-event-to-eventhub/show-result.png)

Normalmente, cria uma aplicação que obtém os eventos do hub de eventos. Para criar uma aplicação que obtém mensagens de um hub de eventos, veja:

* [Começar a receber mensagens com o Anfitrião do Processador de Eventos em .NET Standard](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* [Receber eventos de Hubs de Eventos do Azure com o Java](../event-hubs/event-hubs-java-get-started-send.md)
* [Receber eventos dos Hubs de Eventos com o Apache Storm](../event-hubs/event-hubs-storm-getstarted-receive.md)

## <a name="clean-up-resources"></a>Limpar os recursos
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
