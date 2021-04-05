---
title: Escala elástica com Cassandra API em Azure Cosmos DB
description: Conheça as opções disponíveis para escalar uma conta Azure Cosmos DB Cassandra API e suas vantagens/desvantagens
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340962"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Dimensionar elasticamente uma conta Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Existem uma variedade de opções para explorar a natureza elástica da API API do Azure Cosmos para Cassandra. Para entender como escalar eficazmente em Azure Cosmos DB, é importante entender como providenciar a quantidade certa de unidades de pedido (RU/s) para responder às exigências de desempenho no seu sistema. Para saber mais sobre unidades de pedido, consulte o artigo [Unidades de Pedido.](request-units.md) 

Para a API Cassandra, pode recuperar a taxa da Unidade de Pedido para consultas individuais utilizando os [SDKs .NET e Java](./find-request-unit-charge-cassandra.md). Isto é útil para determinar a quantidade de RU/s que você precisará para prestar no serviço.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operações de base de dados consomem Unidades de Pedido" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Limitação da taxa de manuseamento (429 erros)

A Azure Cosmos DB devolverá erros limitados (429) se os clientes consumirem mais recursos (RU/s) do que a quantidade que adquirou. A API Cassandra em Azure Cosmos DB traduz estas exceções a erros sobrecarregados no protocolo nativo de Cassandra. 

Se o seu sistema não for sensível à latência, pode ser suficiente para lidar com a limitação da taxa de produção utilizando recaídas. Consulte a amostra de [código Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) para como lidar com a limitação da taxa de forma transparente utilizando a extensão DB do [Azure Cosmos](https://github.com/Azure/azure-cosmos-cassandra-extensions) para [a política de relagem de Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) em Java. Também pode utilizar a [extensão Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) para lidar com a limitação da taxa.

## <a name="manage-scaling"></a>Gerir a escala

Se precisar de minimizar a latência, existe um espectro de opções para gerir a escala e o fornecimento de produção (RUs) na API de Cassandra:

* [Manualmente, utilizando o portal Azure](#use-azure-portal)
* [Programáticamente usando as características do plano de controlo](#use-control-plane)
* [Programáticamente usando comandos CQL com um SDK específico](#use-cql-queries)
* [Dinamicamente, utilizando a autoescala](#use-autoscale)

As seguintes secções explicam as vantagens e desvantagens de cada abordagem. Pode então decidir qual a melhor estratégia para equilibrar as necessidades de escala do seu sistema, o custo global e as necessidades de eficiência para a sua solução.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Use o portal Azure

Você pode escalar os recursos na conta Azure Cosmos DB Cassandra API usando o portal Azure. Para saber mais, consulte o artigo sobre [a produção de provisões em contentores e bases de dados.](set-throughput.md) Este artigo explica os benefícios relativos da fixação de produção em qualquer [base de dados](set-throughput.md#set-throughput-on-a-database) ou nível [de contentor](set-throughput.md#set-throughput-on-a-container) no portal Azure. Os termos "base de dados" e "contentor" mencionados nestes artigos mapeiam para "keyspace" e "mesa" respectivamente para a API de Cassandra.

A vantagem deste método é que é uma forma simples de gerir a capacidade de produção na base de dados. No entanto, a desvantagem é que, em muitos casos, a sua abordagem ao escalonamento pode exigir que certos níveis de automação sejam rentáveis e de alto desempenho. As próximas secções explicam os cenários e métodos relevantes.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Use o plano de controlo

A API da Azure Cosmos DB para a Cassandra fornece a capacidade de ajustar a produção programática usando as nossas várias funcionalidades de control-plane. Consulte os [artigos Azure Resource Manager](./templates-samples-cassandra.md), [PowerShell](powershell-samples.md)e [Azure CLI](cli-samples.md) para obter orientação e amostras.

A vantagem deste método é que você pode automatizar a escala de recursos para cima ou para baixo de recursos com base em um temporizador para explicar a atividade de pico, ou períodos de baixa atividade. Veja [aqui](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) a nossa amostra para saber como realizar isto usando as Funções Azure e a PowerShell.

Uma desvantagem com esta abordagem pode ser o facto de não se poder responder a necessidades de mudança imprevisíveis em tempo real. Em vez disso, poderá ter de aproveitar o contexto de aplicação no seu sistema, ao nível do cliente/SDK, ou utilizando a [Autoscale.](provision-throughput-autoscale.md)

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Use consultas CQL com um SDK específico

Pode escalar o sistema dinamicamente com o código executando os [comandos CQL ALTER](cassandra-support.md#keyspace-and-table-options) para a base de dados ou recipiente dado.

A vantagem desta abordagem é que permite responder às necessidades de escala dinamicamente e de uma forma personalizada que se adequa à sua aplicação. Com esta abordagem, você ainda pode aproveitar os encargos e tarifas ru/s padrão. Se as necessidades de escala do seu sistema forem na sua maioria previsíveis (cerca de 70% ou mais), a utilização de SDK com CQL pode ser um método mais rentável de auto-dimensionamento do que a utilização de autoescala. A desvantagem desta abordagem é que pode ser bastante complexo implementar as retró concretizâncias, enquanto a limitação das taxas pode aumentar a latência.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Utilizar a produção de autoescala a provisionada

Além da forma padrão (manual) ou programática de provisão de produção, também pode configurar recipientes Azure cosmos em produção de autoescala. A autoescala será automaticamente e instantaneamente dimensionada para as suas necessidades de consumo dentro de gamas RU especificadas sem comprometer as SLAs. Para saber mais, consulte os [recipientes e bases de dados Create Azure Cosmos em artigo de autoescalação.](provision-throughput-autoscale.md)

A vantagem desta abordagem é que é a forma mais fácil de gerir as necessidades de escala no seu sistema. Não aplicará a limitação das taxas **dentro das gamas RU configuradas**. A desvantagem é que, se as necessidades de escala no seu sistema forem previsíveis, a autoescala pode ser uma forma menos rentável de lidar com as suas necessidades de escala do que usar o plano de controlo sob medida ou abordagens de nível SDK acima mencionadas.

Para definir ou alterar a potência máxima (RUs) para a autoescala utilizando o CQL, utilize o seguinte (substituindo o teclado/nome da tabela em conformidade):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Passos seguintes

- Começar com a [criação de uma conta API cassandra, base de dados e uma tabela](create-cassandra-api-account-java.md) usando uma aplicação Java