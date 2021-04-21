---
title: Ativar sessões de mensagens de ônibus de serviço Azure | Microsoft Docs
description: Este artigo explica como ativar sessões de mensagens usando o portal Azure, PowerShell, CLI e linguagens de programação (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755247"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Ativar sessões de mensagens para uma fila de autocarros do Serviço Azure ou uma subscrição
As sessões de autocarro de serviço Azure permitem o manuseamento conjunto e ordenado de sequências não ilimitadas de mensagens relacionadas. As sessões podem ser usadas **em primeiro lugar, primeiro fora (FIFO)** e padrões **de resposta a pedidos.** Para mais informações, consulte [as sessões de Mensagem](message-sessions.md). Este artigo mostra-lhe diferentes formas de ativar sessões para uma fila ou subscrição de Service Bus. 

> [!IMPORTANT]
> - O nível básico do Service Bus não suporta sessões. As sessões de suporte standard e premium. Para obter diferenças entre estes níveis, consulte [os preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
> - Não é possível ativar ou desativar sessões de mensagens após a criação da fila ou subscrição. Só o pode fazer no momento da criação da fila ou subscrição. 

## <a name="using-azure-portal"></a>Com o Portal do Azure
Ao criar uma **fila** no portal Azure, selecione **Ative sessions** como mostrado na imagem seguinte. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Habilitar a sessão no momento da criação da fila":::

Ao criar uma subscrição para um tópico no portal Azure, selecione **Ative sessions** como mostrado na imagem seguinte. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Habilitar a sessão no momento da criação da subscrição":::

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para **criar uma fila com sessões de mensagens ativadas,** utilize o comando com definido para [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-session` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Para **criar uma subscrição para um tópico com sessões de mensagens ativadas,** utilize o [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) comando com definido para `--enable-session` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Para **criar uma fila com sessões de mensagens ativadas,** utilize o comando com definido para [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresSession` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Para **criar uma subscrição para um tópico com sessões de mensagens ativadas,** utilize o [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) comando com definido para `-RequiresSession` `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
Para **criar uma fila com sessões de mensagens ativadas,** desavere-se na secção de propriedades da `requiresSession` `true` fila. Para obter mais informações, consulte [os espaços de nomes/filas do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Para **criar uma subscrição para um tópico com sessões de mensagens ativadas,** definido `requiresSession` na secção de propriedades de `true` subscrição. Para obter mais informações, consulte [os espaços de nomes/tópicos/subscrições do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
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
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "requiresSession": true
              }
            }
          ]
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