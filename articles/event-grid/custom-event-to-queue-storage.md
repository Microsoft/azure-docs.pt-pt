---
title: 'Início rápido: enviar eventos personalizados para fila de armazenamento-grade de eventos, CLI do Azure'
description: 'Início rápido: Use a grade de eventos do Azure e CLI do Azure para publicar um tópico e assinar esse evento. É utilizada uma fila de armazenamento para o ponto final.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 44ffa570cfda39c186966866ee5755ab090083a5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721444"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Início rápido: rotear eventos personalizados para o armazenamento de filas do Azure com CLI do Azure e a grade de eventos

O Azure Event Grid é um serviço de eventos para a cloud. O armazenamento de Filas do Azure é um dos processadores de eventos suportados. Neste artigo, a CLI do Azure serve para criar um tópico personalizado, subscrever o tópico personalizado e acionar o evento para ver o resultado. Os eventos são enviados para o armazenamento de Filas.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você estiver usando CLI do Azure ou Azure PowerShell em seu computador local em vez de usar Cloud Shell no portal do Azure, verifique se você tem as seguintes versões do CLI do Azure e Azure PowerShell. 

- CLI do Azure versão 2.0.56 ou superior. Para obter instruções sobre como instalar a versão mais recente do CLI do Azure, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli). 
- Azure PowerShell versão 1.1.0 ou superior. Baixe a versão mais recente do Azure PowerShell em seu computador Windows por meio de [downloads do Azure – ferramentas de linha de comando](https://azure.microsoft.com/downloads/). 

Este artigo fornece comandos para usar CLI do Azure. 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). 

O exemplo seguinte cria um grupo de recursos com o nome *gridResourceGroup* na localização *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. O exemplo seguinte cria o tópico personalizado no seu grupo de recursos. Substitua `<topic_name>` por um nome exclusivo para o seu tópico personalizado. O nome do tópico do Event Grid deve ser exclusivo, porque é representado por uma entrada DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>Criar armazenamento de Filas

Antes de subscrever o tópico personalizado, vamos criar o ponto final para a mensagem de evento. Cria um armazenamento de Filas para a recolha de eventos.

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-custom-topic"></a>Subscrever um tópico personalizado

Você assina um tópico personalizado para informar à grade de eventos quais eventos você deseja rastrear. O exemplo a seguir assina o tópico personalizado que você criou e passa a ID de recurso do armazenamento de fila para o ponto de extremidade. Com a CLI do Azure, transmite o ID de armazenamento da Fila como ponto final. O ponto final está no formato:

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

O script seguinte obtém o ID do recurso da conta de armazenamento para a fila. Constrói o ID para o armazenamento de filas e subscreve um tópico do Event Grid. Define o tipo de ponto final `storagequeue` e utiliza o ID de fila para o ponto final.

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid \
  --expiration-date "<yyyy-mm-dd>"
```

A conta que cria a subscrição de evento deve ter acesso de escrita ao armazenamento de filas. Observe que está definida uma [data de expiração](concepts.md#event-subscription-expiration) para a subscrição.

Se utilizar a API REST para criar a subscrição, transmite o ID da conta de armazenamento e o nome da fila como um parâmetro separado.

```json
"destination": {
  "endpointType": "storagequeue",
  "properties": {
    "queueName":"eventqueue",
    "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>"
  }
  ...
```

## <a name="send-an-event-to-your-custom-topic"></a>Enviar um evento para o tópico personalizado

Vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Em primeiro lugar, vamos obter o URL e a chave do tópico personalizado. Novamente, utilize o nome do tópico personalizado de `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artigo, vai utilizar dados do evento de exemplo para enviar para o tópico personalizado. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento. CURL é um utilitário que envia os pedidos HTTP. Neste artigo, utilize o CURL para enviar o evento para o tópico personalizado.  O exemplo seguinte envia três eventos para o tópico do Event Grid:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Navegue para o armazenamento de Filas no portal e repare que o Event Grid enviou esses três eventos para a fila.

![Mostrar mensagens](./media/custom-event-to-queue-storage/messages.png)


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
