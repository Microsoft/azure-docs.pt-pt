---
title: Ativar a deteção de mensagens duplicadas - Azure Service Bus
description: Este artigo explica como permitir a deteção de mensagens duplicadas utilizando o portal Azure, PowerShell, CLI e linguagens de programação (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755252"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Ativar a deteção de mensagens duplicadas para uma fila de autocarros da Azure Service ou um tópico
Quando ativa a deteção duplicada para uma fila ou tópico, a Azure Service Bus mantém um histórico de todas as mensagens enviadas para a fila ou tópico durante um período de tempo de configuração. Durante esse intervalo, a sua fila ou tópico não armazenam nenhuma mensagem duplicada. Ativar esta propriedade garante exatamente uma vez a entrega ao longo de um período de tempo definido pelo utilizador. Para mais informações, consulte [a deteção de Duplicado.](duplicate-detection.md) Este artigo mostra-lhe diferentes formas de permitir a deteção de mensagens duplicadas para uma fila de ônibus de serviço ou um tópico. 


> [!NOTE]
> - O nível básico do Service Bus não suporta a deteção de duplicados. Os níveis standard e premium suportam a deteção duplicada. Para obter diferenças entre estes níveis, consulte [os preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
> - Não é possível ativar ou desativar a deteção duplicada após a criação da fila ou do tópico. Só pode fazê-lo no momento de criar a fila ou o tópico. 

## <a name="using-azure-portal"></a>Com o Portal do Azure
Ao criar uma **fila** no portal Azure, selecione **Ative a deteção de duplicação** como mostrado na imagem seguinte. Pode configurar o tamanho da janela de deteção duplicada ao criar uma fila ou tópico. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Permitir a deteção duplicada no momento da criação da fila":::

Ao criar um tópico no portal Azure, selecione **Ative a deteção duplicada** como mostrado na imagem seguinte. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Permitir a deteção duplicada no momento da criação do tópico":::

Também pode configurar esta definição para uma fila ou tópico existente, se tivesse ativado a deteção duplicada no momento da criação. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Atualizar duplicar o tamanho da janela de deteção para uma fila existente ou um tópico
Para alterar o tamanho da janela de deteção duplicada para uma fila existente ou um tópico, na página **'Vista Geral',** selecione **Alterar** para **janela de deteção duplicada**.  

#### <a name="queue"></a>Fila
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Definir tamanho da janela de deteção duplicada para uma fila":::

#### <a name="topic"></a>Tópico
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Definir tamanho da janela de deteção duplicada para um tópico":::


## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para **criar uma fila com deteção duplicada ativada,** utilize o comando com definido para [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-duplicate-detection` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Para **criar um tópico com deteção duplicada ativada,** utilize o comando com definido para [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-duplicate-detection` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Os exemplos acima também definem o tamanho da janela de deteção duplicada utilizando o `--duplicate-detection-history-time-window` parâmetro. O tamanho da janela está definido para um dia. O valor predefinido é de 10 minutos e o valor máximo permitido é de sete dias.

Para **atualizar uma fila com um novo tamanho de janela de deteção,** utilize o [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) comando com o `--duplicate-detection-history-time-window` parâmetro. Neste exemplo, o tamanho da janela é atualizado para sete dias. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Da mesma forma, para **atualizar um tópico com um novo tamanho de janela de deteção,** use o [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) comando com o `--duplicate-detection-history-time-window` parâmetro. Neste exemplo, o tamanho da janela é atualizado para sete dias.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Para **criar uma fila com deteção duplicada ativada,** utilize o comando com definido para [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresDuplicateDetection` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Para **criar um tópico com deteção duplicada ativada,** utilize o comando com definido para [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-RequiresDuplicateDetection` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

Os exemplos acima também definem o tamanho da janela de deteção duplicada utilizando o `-DuplicateDetectionHistoryTimeWindow` parâmetro. O tamanho da janela está definido para um dia. O valor predefinido é de 10 minutos e o valor máximo permitido é de sete dias.

Para **atualizar uma fila com um novo tamanho da janela de deteção,** consulte o exemplo seguinte.  Neste exemplo, o tamanho da janela é atualizado para sete dias.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Para **atualizar um tópico com um novo tamanho da janela de deteção,** consulte o exemplo seguinte. Neste exemplo, o tamanho da janela é atualizado para sete dias.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
Para **criar uma fila com deteção duplicada ativada,** definida na secção de propriedades da `requiresDuplicateDetection` `true` fila. Para obter mais informações, consulte [os espaços de nomes/filas do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) Especifique um valor para a `duplicateDetectionHistoryTimeWindow` propriedade para definir o tamanho da janela de deteção duplicada. No exemplo seguinte, está definido para um dia.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Para **criar um tópico com deteção duplicada ativado,** definido para na secção de propriedades `requiresDuplicateDetection` `true` tópicos. Para obter mais informações, consulte [os espaços de nomes/tópicos do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Passos seguintes
Experimente as amostras no idioma à sua escolha para explorar as funcionalidades do Azure Service Bus. 

- [Amostras da biblioteca de clientes do Azure Service Bus para Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Amostras da biblioteca do cliente do Azure Service Bus para Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Amostras de biblioteca de clientes do Azure Service Bus para JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Amostras de biblioteca de clientes do Azure Service Bus para TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Amostras de Azure.Messaging.ServiceBus para .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Encontre amostras para as bibliotecas clientes mais antigas .NET e Java abaixo:
- [Amostras microsoft.Azure.ServiceBus para .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [amostras de azure-servicebus para Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)