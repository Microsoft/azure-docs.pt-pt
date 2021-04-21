---
title: Ativar o encaminhamento automático para filas e subscrições do Azure Service Bus
description: Este artigo explica como permitir o encaminhamento automático para filas e subscrições utilizando o portal Azure, PowerShell, CLI e linguagens de programação (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ef22ae08485dc896c94858db4e422cf89a00ec1f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755282"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Ativar o encaminhamento automático para filas e subscrições do Azure Service Bus
A funcionalidade de encaminhamento automático do Service Bus permite-lhe acorrentar uma fila ou subscrição a outra fila ou tópico que faça parte do mesmo espaço de nome. Quando o encaminhamento automático está ativado, o Service Bus remove automaticamente as mensagens que são colocadas na primeira fila ou subscrição (fonte) e coloca-as na segunda fila ou tópico (destino). Ainda é possível enviar uma mensagem diretamente à entidade de destino. Para mais informações, consulte [as entidades chaining Service Bus com reencaminhamento automático.](service-bus-auto-forwarding.md) Este artigo mostra-lhe diferentes formas de permitir o encaminhamento automático para filas e subscrições de Service Bus. 

> [!IMPORTANT]
> O nível básico do Service Bus não suporta a função de reencaminhamento automático. Os níveis standard e premium suportam a funcionalidade. Para obter diferenças entre estes níveis, consulte [os preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-azure-portal"></a>Com o Portal do Azure
Ao criar uma **fila** ou uma **subscrição** para um tópico no portal Azure, selecione **mensagens de encaminhar para fila/tópico,** como mostrado nos seguintes exemplos. Em seguida, especifique se deseja que as mensagens sejam reencaminhadas para uma fila ou um tópico. Neste exemplo, a opção **Fila** é selecionada e uma fila (**myqueue**) do mesmo espaço de nome é selecionada.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Criar uma fila com encaminhamento automático ativado
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Ativar o auto-forward no momento da criação da fila":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Crie uma subscrição para um tópico com encaminhamento automático ativado
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Ativar o auto-forward no momento da criação da subscrição":::

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para **criar uma fila com o reencaminhamento automático ativado,** utilize o comando com o nome da fila ou tópico para o qual pretende que as [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` mensagens sejam reencaminhadas. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Para **criar uma subscrição para um tópico com reencaminhamento automático ativado,** utilize o [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) comando com o nome da fila ou tópico para o qual pretende que as `--forward-to` mensagens sejam reencaminhadas.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Para **criar uma fila com o reencaminhamento automático ativado,** utilize o comando com o nome da fila ou tópico para o qual pretende que as [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` mensagens sejam reencaminhadas. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Para **criar uma subscrição para um tópico com reencaminhamento automático ativado,** utilize o [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) comando com o nome da fila ou tópico para o qual pretende que as `-ForwardTo` mensagens sejam reencaminhadas.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
Para **criar uma fila com reencaminhamento automático ativado,** desa ajuste na secção de propriedades da fila para o nome da fila ou tópico para o qual pretende que as `forwardTo` mensagens sejam reencaminhadas. Para obter mais informações, consulte [os espaços de nomes/filas do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Para **criar uma subscrição para um tópico com reencaminhamento automático ativado**, de definida `forwardTo` na secção de propriedades de fila para o nome da fila ou tópico para o qual pretende que as mensagens sejam reencaminhadas. Para obter mais informações, consulte [os espaços de nomes/tópicos/subscrições do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "forwardTo": "myqueue2"
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