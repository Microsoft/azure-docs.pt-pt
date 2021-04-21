---
title: Ativar a inscrição morta para filas e subscrições do Azure Service Bus
description: Este artigo explica como permitir a inscrição morta para filas e subscrições utilizando o portal Azure, PowerShell, CLI e linguagens de programação (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819404"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Ativar a inscrição morta na expiração da mensagem para as filas e subscrições do Azure Service Bus
As filas e subscrições de ônibus da Azure Service para tópicos fornecem uma subqueue secundária, chamada uma fila de letras mortas (DLQ). A fila de cartas mortas não precisa de ser explicitamente criada e não pode ser eliminada ou gerida independentemente da entidade principal. O objetivo da fila da carta morta é guardar mensagens que não podem ser entregues a nenhum recetor, ou mensagens que não possam ser processadas. Para mais informações, consulte [as filas de cartas mortas do Service Bus.](service-bus-dead-letter-queues.md) Este artigo mostra-lhe diferentes formas de permitir a inscrição morta para filas e subscrições de Service Bus. 

## <a name="using-azure-portal"></a>Com o Portal do Azure
Ao criar uma **fila** ou **subscrição** para um tópico no portal Azure, selecione Ative a letra morta na expiração da **mensagem,** como mostrado nos seguintes exemplos. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Criar uma fila com letras mortas ativadas
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Habilitar a inscrição morta no momento da criação da fila":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Criar uma subscrição com letras mortas ativadas
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Habilitar a inscrição morta no momento da criação da subscrição":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Atualize a inscrição morta na definição de expiração da mensagem para uma fila existente
Na página **'Vista Geral'** para a sua fila de ônibus de serviço, selecione o valor atual para a **rotulação Dead** na definição de expiração da mensagem. No exemplo seguinte, o valor atual é **desativado.** Pode ativar ou desativar a letra morta na expiração da mensagem na janela popup. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Ativar a inscrição morta na expiração da mensagem para uma fila existente":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Atualizar a inscrição morta na definição de expiração da mensagem para uma subscrição existente
Na página **'Vista Geral'** para a subscrição do Service Bus, selecione o valor atual para a **rotulação Dead** na definição de expiração da mensagem. No exemplo seguinte, o valor atual é **desativado.** Pode ativar ou desativar a letra morta na expiração da mensagem na janela popup. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Ativar a inscrição morta na expiração da mensagem para uma subscrição existente":::

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para **criar uma fila com letras mortas na expiração da mensagem ativada,** utilize o comando com definido para [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Para **ativar a inscrição morta na definição de expiração da mensagem para uma fila existente,** utilize o [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) comando com definido para `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Para **criar uma subscrição de um tópico com letras mortas na expiração da mensagem ativada,** utilize o [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) comando com definido para `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Para **ativar a inscrição morta na definição de expiração da mensagem para uma subscrição de um tópico,** utilize o [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) comando com conjunto `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Para **criar uma fila com letras mortas na expiração da mensagem ativada,** utilize o comando com definido para [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Para **ativar a inscrição morta na definição de expiração da mensagem para uma fila existente,** utilize o [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) comando como mostrado no exemplo seguinte.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Para **criar uma subscrição para um tópico com letras mortas na expiração da mensagem ativada,** utilize o [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) comando com definido para `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Para **ativar a inscrição morta na definição de expiração da mensagem para uma subscrição existente,** consulte o exemplo seguinte.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
Para **criar uma fila com letras mortas na expiração da mensagem ativada,** coloque `deadLetteringOnMessageExpiration` na secção de propriedades de fila para `true` . Para obter mais informações, consulte [os espaços de nomes/filas do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Para **criar uma subscrição para um tópico com letras mortas na expiração da mensagem ativada,** definida `deadLetteringOnMessageExpiration` na secção de propriedades de fila para `true` . Para obter mais informações, consulte [os espaços de nomes/tópicos/subscrições do Microsoft.ServiceBus.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "deadLetteringOnMessageExpiration": true
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