---
title: Adicione dinamicamente divisórias a um centro de eventos em Azure Event Hubs
description: Este artigo mostra-lhe como adicionar dinamicamente divisórias a um centro de eventos em Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 4a729147eaa11497c66f82a9764dfee9492786b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87002544"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Adicione dinamicamente divisórias a um centro de eventos (tema Apache Kafka) em Azure Event Hubs
O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos. Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que os eventos mais recentes chegam, são adicionados ao fim desta sequência. Para obter mais informações sobre divisórias em geral, consulte [As Partições](event-hubs-scalability.md#partitions)

Pode especificar o número de divisórias no momento da criação de um centro de eventos. Em alguns cenários, poderá ter de adicionar divisórias após a criação do centro de eventos. Este artigo descreve como adicionar dinamicamente as divisórias a um centro de eventos existente. 

> [!IMPORTANT]
> Adições dinâmicas de divisórias só estão disponíveis em clusters de Centros de Eventos **Dedicados.**

> [!NOTE]
> Para os clientes Apache Kafka, um **centro de eventos** mapeia um **tópico kafka.** Para mais mapeamentos entre Azure Event Hubs e Apache Kafka, consulte [o mapeamento conceptual de Kafka e Event Hubs](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Atualizar a contagem de divisórias
Esta secção mostra-lhe como atualizar a contagem de partição de um centro de eventos de diferentes maneiras (PowerShell, CLI, e assim por diante).).

### <a name="powershell"></a>PowerShell
Utilize o comando [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub?view=azurermps-6.13.0) PowerShell para atualizar as divisórias num centro de eventos. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Utilize o [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub?view=azure-cli-latest#az-eventhubs-eventhub-update) comando CLI para atualizar as divisórias num centro de eventos. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Atualizar o valor da `partitionCount` propriedade no modelo de Gestor de Recursos e recolocar o modelo para atualizar o recurso. 

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
Utilize a `AlterTopics` API (por exemplo, através da ferramenta CLI **de tópicos kafka)** para aumentar a contagem de divisórias. Para mais detalhes, consulte [modificar os tópicos de Kafka.](http://kafka.apache.org/documentation/#basic_ops_modify_topic) 

## <a name="event-hubs-clients"></a>Clientes do Event Hubs
Vamos ver como os clientes do Event Hubs se comportam quando a contagem de divisórias é atualizada num centro de eventos. 

Quando adiciona uma partição a um hub uniforme existente, o cliente do centro de eventos recebe um `MessagingException` do serviço informando os clientes de que os metadados de entidade (entidade é o seu centro de eventos e metadados são a informação de partição) foram alterados. Os clientes reabrirão automaticamente as ligações AMQP, que recolheriam as informações de metadados alteradas. Os clientes funcionam normalmente.

### <a name="senderproducer-clients"></a>Clientes remetentes/produtores
O Event Hubs oferece três opções de remetente:

- **Remetente de partição** – Neste cenário, os clientes enviam eventos diretamente para uma partição. Embora as divisórias sejam identificáveis e os eventos possam ser enviados diretamente para eles, não recomendamos este padrão. Adicionar divisórias não afeta este cenário. Recomendamos que reinicie as aplicações para que possam detetar as novas divisórias adicionadas. 
- **Remetente chave de partição** – neste cenário, os clientes enviam os eventos com uma chave para que todos os eventos pertencentes a essa chave acabem na mesma partição. Neste caso, o serviço tem a chave e as rotas para a partição correspondente. A atualização da contagem de divisórias pode causar problemas fora de ordem devido a alterações de hashing. Por isso, se se preocupa em encomendar, certifique-se de que a sua aplicação consome todos os eventos das divisórias existentes antes de aumentar a contagem de divisórias.
- **Remetente round-robin (padrão)** – Neste cenário, o serviço Event Hubs roda os eventos através de divisórias. O serviço Event Hubs está ciente das alterações na contagem de divisórias e enviará para novas divisórias poucos segundos após alterar a contagem de divisórias.

### <a name="receiverconsumer-clients"></a>Clientes recetores/consumidores
O Event Hubs fornece recetores diretos e uma biblioteca de consumidores fácil chamada [Host de Processador de Eventos (antigo SDK)](event-hubs-event-processor-host.md)  ou Processador de [Eventos (novo SDK)](event-processor-balance-partition-load.md).

- **Recetores diretos** – Os recetores diretos ouvem divisórias específicas. O seu comportamento de execução não é afetado quando as divisórias são dimensionadas para um centro de eventos. A aplicação que utiliza recetores diretos tem de cuidar da recolha das novas divisórias e da atribuição dos recetores em conformidade.
- **Anfitrião do processador de eventos** – Este cliente não atualiza automaticamente os metadados da entidade. Então, não iria captar o aumento da contagem de divisórias. Recriar uma instância de processador de eventos irá causar uma busca de metadados de entidade, que por sua vez criará novas bolhas para as novas divisórias adicionadas. As bolhas pré-existentes não serão afetadas. Recomenda-se o reinício de todas as instâncias do processador de eventos para garantir que todas as instâncias estejam cientes das divisórias recém-adicionadas e que o equilíbrio de carga seja manuseado corretamente entre os consumidores.

    Se estiver a utilizar a versão antiga de .NET SDK[(WindowsAzure.ServiceBus),](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)o anfitrião do processador de eventos remove um ponto de verificação existente no recomeço se a contagem de divisórias no ponto de verificação não corresponder à contagem de divisórias recolhida do serviço. Este comportamento pode ter um impacto na sua aplicação. 

## <a name="apache-kafka-clients"></a>Clientes Apache Kafka
Esta secção descreve como os clientes Apache Kafka que usam o ponto final kafka dos Azure Event Hubs se comportam quando a contagem de divisórias é atualizada para um centro de eventos. 

Os clientes Kafka que usam Os Centros de Eventos com o protocolo Apache Kafka comportam-se de forma diferente dos clientes do centro de eventos que usam o protocolo AMQP. Os clientes kafka atualizam os seus metadados uma vez a cada `metadata.max.age.ms` milissegundo. Especifica este valor nas configurações do cliente. As `librdkafka` bibliotecas também usam a mesma configuração. As atualizações de metadados informam os clientes das alterações de serviço, incluindo o aumento da contagem de divisórias. Para obter uma lista de configurações, consulte [as configurações apache Kafka para Centros de Eventos](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Clientes remetentes/produtores
Os produtores ditam sempre que os pedidos de envio contenham o destino de partição para cada conjunto de registos produzidos. Assim, todas as divisórias de produção são feitas do lado do cliente com a visão do produtor dos metadados do corretor. Assim que as novas divisórias forem adicionadas à visão de metadados do produtor, estarão disponíveis para pedidos de produtor.

### <a name="consumerreceiver-clients"></a>Clientes de consumo/recetor
Quando um membro do grupo de consumidores executa uma atualização de metadados e apanha as divisórias recém-criadas, esse membro inicia um reequilíbrio de grupo. Os metadados de consumo serão então atualizados para todos os membros do grupo, e as novas divisórias serão atribuídas pelo líder de reequilíbrio atribuído.

## <a name="recommendations"></a>Recomendações

- Se utilizar a chave de partição com as aplicações do produtor e depender do hashing da chave para garantir a encomenda numa partição, não é recomendada a adição dinâmica de divisórias. 

    > [!IMPORTANT]
    > Enquanto os dados existentes preservam a encomenda, o hashing da partição será quebrado para mensagens hashed após a contagem de divisórias mudar devido à adição de divisórias.
- A adição de divisórias a um tópico ou exemplo de centro de eventos existente é recomendada nos seguintes casos:
    - Quando se usa o método de rodada de robin (predefinido) de envio de eventos
     - Estratégias de partição padrão de Kafka, exemplo – Estratégia de Atribuição Pegajosa


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre divisórias, consulte [As Partições.](event-hubs-scalability.md#partitions)

