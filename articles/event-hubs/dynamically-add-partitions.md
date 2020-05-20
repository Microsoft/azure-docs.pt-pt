---
title: Dinamicamente adicionar divisórias a um centro de eventos em Azure Event Hubs
description: Este artigo mostra-lhe como adicionar divisórias dinâmicas a um centro de eventos em Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/23/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 0fc1a29aa34be8e692a92c7c1cfb73b5c22b037d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665188"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Adicione divisórias dinâmicas a um centro de eventos (tópico Apache Kafka) em Hubs de Eventos Azure
O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos. Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que os eventos mais recentes chegam, são adicionados ao fim desta sequência. Para obter mais informações sobre divisórias em geral, consulte [Divisórias](event-hubs-scalability.md#partitions)

Pode especificar o número de divisórias no momento da criação de um centro de eventos. Em alguns cenários, poderá ter de adicionar divisórias após a criação do centro do evento. Este artigo descreve como adicionar divisórias dinamicamente a um centro de eventos existente. 

> [!IMPORTANT]
> Adições dinâmicas de divisórias só estão disponíveis em clusters **dedicados** de Centros de Eventos.

> [!NOTE]
> Para os clientes Apache Kafka, um centro de **eventos** mapeia um **tópico kafka.** Para mais mapeamentos entre Os Centros de Eventos Azure e Apache Kafka, consulte [kafka e event hubs conceptmap](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Atualizar a contagem de partição
Esta secção mostra-lhe como atualizar a contagem de divisórias de um centro de eventos de diferentes maneiras (PowerShell, CLI, e assim por diante.).

### <a name="powershell"></a>PowerShell
Utilize o comando [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub?view=azurermps-6.13.0) PowerShell para atualizar as divisórias num centro de eventos. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Utilize o comando CLI de atualização de atualização de [eventhubs az eventhubs](/cli/azure/eventhubs/eventhub?view=azure-cli-latest#az-eventhubs-eventhub-update) para atualizar divisórias num centro de eventos. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Atualizar o valor da propriedade no modelo De Gestor de `partitionCount` Recursos e recolocar o modelo para atualizar o recurso. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Utilize a `AlterTopics` API (por exemplo, através da ferramenta CLI de **tópicos kafka)** para aumentar a contagem de divisórias. Para mais detalhes, consulte [modificar tópicos de Kafka.](http://kafka.apache.org/documentation/#basic_ops_modify_topic) 

## <a name="event-hubs-clients"></a>Clientes do Event Hubs
Vamos ver como os clientes do Event Hubs se comportam quando a contagem de partições é atualizada num centro de eventos. 

Quando adiciona uma partição a um hub existente, o cliente do hub de eventos recebe uma "Reexceção de Mensagens" do serviço informando os clientes de que os metadados da entidade (entidade é o seu hub de eventos e metadados são a informação de partição) foi alterado. Os clientes reabrirão automaticamente as ligações AMQP, que depois recolheriam as informações alteradas dos metadados. Os clientes operam normalmente.

### <a name="senderproducer-clients"></a>Clientes remetentes/produtores
O Event Hubs oferece três opções de remetente:

- **Remetente de partição** – Neste cenário, os clientes enviam eventos diretamente para uma partição. Embora as divisórias sejam identificáveis e os eventos possam ser enviados diretamente para eles, não recomendamos este padrão. Adicionar divisórias não afeta este cenário. Recomendamos que reinicie as aplicações para que possam detetar divisórias recém-adicionadas. 
- **Remetente chave de partição** – neste cenário, os clientes enviam os eventos com uma chave para que todos os eventos pertencentes a essa chave acabem na mesma divisória. Neste caso, o serviço apressa a chave e as rotas para a divisória correspondente. A atualização da contagem de divisórias pode causar problemas fora de ordem devido a alterações de hashing. Por isso, se se preocupa em encomendar, certifique-se de que a sua aplicação consome todos os eventos a partir de divisórias existentes antes de aumentar a contagem de partições.
- **Remetente de robin redondo (padrão)** – Neste cenário, o serviço De Eventos Hubs gira os eventos em divisórias. O serviço De Event Hubs está ciente das alterações na contagem de divisórias e enviará para novas divisórias segundos após alterar a contagem de divisórias.

### <a name="receiverconsumer-clients"></a>Clientes recetores/consumidores
O Event Hubs fornece recetores diretos e uma biblioteca de consumidores fácil chamada Anfitrião do Processador de [Eventos (sDK antigo)](event-hubs-event-processor-host.md) ou processador de [eventos (novo SDK)](event-processor-balance-partition-load.md).

- **Recetores diretos** – Os recetores diretos ouvem divisórias específicas. O seu comportamento de tempo de corrido não é afetado quando as divisórias são dimensionadas para um centro de eventos. A aplicação que utiliza recetores diretos tem de cuidar da recolha das novas divisórias e da atribuição dos recetores em conformidade.
- **Anfitrião** do processador de eventos – Este cliente não atualiza automaticamente os metadados da entidade. Então, não iria aumentar a contagem de partição. A recriação de uma instância de processador de eventos causará uma busca de metadados de entidades, o que por sua vez irá criar novas bolhas para as divisórias recém-adicionadas. Bolhas pré-existentes não serão afetadas. É aconselhável reiniciar todas as instâncias do processador de eventos para garantir que todas as instâncias estão cientes das divisórias recém-adicionadas, e o equilíbrio de carga é manuseado corretamente entre os consumidores.

    Se estiver a utilizar a versão antiga do .NET SDK ([WindowsAzure.ServiceBus),](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)o anfitrião do processador do evento remove um ponto de verificação existente no momento do reinício se a contagem de divisórias no ponto de verificação não corresponder à contagem de divisórias recolhida do serviço. Este comportamento pode ter um impacto na sua aplicação. 

## <a name="apache-kafka-clients"></a>Clientes Apache Kafka
Esta secção descreve como os clientes Apache Kafka que usam o ponto final de Kafka dos Hubs de Eventos Azure se comportam quando a contagem de partições é atualizada para um centro de eventos. 

Os clientes kafka que usam Os Hubs de Eventos com o protocolo Apache Kafka comportam-se de forma diferente dos clientes do hub de eventos que utilizam o protocolo AMQP. Os clientes kafka atualizam os seus metadados uma vez a cada `metadata.max.age.ms` milissegundos. Especifica este valor nas configurações do cliente. As `librdkafka` bibliotecas também usam a mesma configuração. As atualizações de metadados informam os clientes das alterações de serviço, incluindo o aumento da contagem de partições. Para obter uma lista de configurações, consulte [configurações apache Kafka para Centros](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md) de Eventos

### <a name="senderproducer-clients"></a>Clientes remetentes/produtores
Os produtores ditam sempre que os pedidos de envio contêm o destino de partição para cada conjunto de registos produzidos. Assim, todas as divisórias de produtos são feitas do lado do cliente com a visão do produtor dos metadados do corretor. Assim que as novas divisórias forem adicionadas à visão de metadados do produtor, estarão disponíveis para pedidos de produtor.

### <a name="consumerreceiver-clients"></a>Clientes consumidores/recetores
Quando um membro do grupo de consumidores executa uma atualização de metadados e recolhe as divisórias recém-criadas, esse membro inicia um reequilíbrio de grupo. Os metadados dos consumidores serão então atualizados para todos os membros do grupo, e as novas divisórias serão atribuídas pelo líder de reequilíbrio atribuído.

## <a name="recommendations"></a>Recomendações

- Se utilizar a chave de partição com as aplicações do produtor e depender do hashing chave para garantir a encomenda numa divisória, não é recomendada a adição dinâmica de divisórias. 

    > [!IMPORTANT]
    > Enquanto os dados existentes preservam a encomenda, o hashing da partição será quebrado para mensagens hashed após a contagem de divisórias mudar devido à adição de divisórias.
- A adição de partição a um tópico ou instância de centro de eventos existente é recomendada nos seguintes casos:
    - Quando utilizar o método de envio de eventos de robin redondo (padrão)
     - Estratégias de partição padrão de Kafka, exemplo – Estratégia StickyAssignor


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre divisórias, consulte [Divisórias](event-hubs-scalability.md#partitions).

