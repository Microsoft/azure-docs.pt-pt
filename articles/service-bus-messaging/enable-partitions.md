---
title: Permitir a partilha nas filas e tópicos do Azure Service Bus
description: Este artigo explica como permitir a partição em filas e tópicos do Azure Service Bus utilizando o portal Azure, PowerShell, CLI e linguagens de programação (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755239"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Ativar a partilha para uma fila de autocarros da Azure Service ou um tópico
As divisórias de ônibus de serviço permitem que filas e tópicos, ou entidades de mensagens, sejam divididos em vários corretores de mensagens e lojas de mensagens. A partilha significa que o rendimento global de uma entidade dividida já não é limitado pelo desempenho de um único corretor de mensagens ou loja de mensagens. Além disso, uma paragem temporária de uma loja de mensagens não torna uma fila ou tópico dividido indisponíveis. Filas e tópicos divididos podem conter todas as funcionalidades avançadas do Service Bus, tais como suporte para transações e sessões. Para mais informações, consulte [filas e tópicos divididos.](service-bus-partitioning.md) Este artigo mostra-lhe diferentes formas de permitir a deteção de mensagens duplicadas para uma fila de ônibus de serviço ou um tópico. 

> [!IMPORTANT]
> - A partilha está disponível na criação de entidades para todas as filas e tópicos em SKUs Básicos ou Standard. Não está disponível para o SKU de mensagens Premium, mas quaisquer entidades divisórias anteriormente existentes em espaços de nome Premium continuam a funcionar como esperado.
> - Não é possível alterar a opção de partição em qualquer fila ou tópico existente. Só pode definir a opção quando criar uma fila ou um tópico. 

## <a name="using-azure-portal"></a>Com o Portal do Azure
Ao criar uma **fila** no portal Azure, selecione **Ativar a partição** como mostrado na imagem seguinte. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Permitir a partilha no momento da criação da fila":::

Ao criar um tópico no portal Azure, selecione **Ativar a partição** como mostrado na imagem a seguir. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Permitir a partilha no momento da criação do tópico":::

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para **criar uma fila com partição ativada,** utilize o comando com definido para [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Para **criar um tópico com partição ativada,** utilize o comando com definido para [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Para **criar uma fila com partição ativada,** utilize o comando com definido para [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-EnablePartitioning` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Para **criar um tópico com partição ativada,** utilize o comando com definido para [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-EnablePartitioning` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
Para **criar uma fila com partição ativada,** desajeita-se na secção de propriedades da `enablePartitioning` `true` fila. Para obter mais informações, consulte [os espaços de nomes/filas do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Para **criar um tópico com deteção duplicada ativado,** definido para na secção de propriedades `enablePartitioning` `true` tópicos. Para obter mais informações, consulte [os espaços de nomes/tópicos do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

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
            "enablePartitioning": true
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