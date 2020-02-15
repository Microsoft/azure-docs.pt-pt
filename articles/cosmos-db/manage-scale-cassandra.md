---
title: Escala elástica com Cassandra API em Azure Cosmos DB
description: Conheça as opções disponíveis para escalar uma conta API Da API da Azure Cosmos DB E as suas vantagens/desvantagens
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 668e9ddadf151a86be0d8c09fc91b4c70db12f3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210791"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Escala elástica uma conta API Da API do Azure Cosmos DB Cassandra

Existem várias opções para explorar a natureza elástica da API do Azure Cosmos DB para Cassandra. Para compreender como escalar eficazmente no Azure Cosmos DB, é importante entender como fornecer a quantidade certa de unidades de pedido (RU/s) para responder às exigências de desempenho no seu sistema. Para saber mais sobre as unidades de pedido, consulte o artigo [unidades de pedido.](request-units.md) 

![Operações de base de dados consomem Unidades de Pedido](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Limitação da taxa de manuseamento (429 erros)

A Azure Cosmos DB devolverá erros limitados à taxa de câmbio (429) se os clientes consumirem mais recursos (RU/s) do que o montante que você provisionou. A Cassandra API em Azure Cosmos DB traduz estas exceções a erros sobrecarregados no protocolo nativo de Cassandra. 

Se o seu sistema não for sensível à latência, pode ser suficiente para lidar com a limitação da taxa de entrada utilizando repetições. Consulte a amostra do [código Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) para saber como lidar com a limitação da taxa de forma transparente utilizando a [extensão Do BD Azure Cosmos](https://github.com/Azure/azure-cosmos-cassandra-extensions) para a política de [retry de Cassandra](https://docs.datastax.com/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) em Java. Também pode utilizar a [extensão Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) para lidar com a limitação da taxa.

## <a name="manage-scaling"></a>Gerir a escala

Se precisar minimizar a latência, existe um espectro de opções para gerir a escala e o fornecimento de reput (RUs) na API Cassandra:

* [Manualmente usando o portal Azure](#use-azure-portal)
* [Programáticamente utilizando as características do plano de controlo](#use-control-plane)
* [Programáticamente utilizando comandos CQL com um SDK específico](#use-cql-queries)
* [Dinamicamente usando o Piloto Automático](#use-autopilot)

As secções seguintes explicam as vantagens e desvantagens de cada abordagem. Em seguida, pode decidir sobre a melhor estratégia para equilibrar as necessidades de escala do seu sistema, o custo global e as necessidades de eficiência para a sua solução.

## <a id="use-azure-portal"></a>Utilize o portal Azure

Pode escalar os recursos na conta API da Azure Cosmos DB Cassandra utilizando o portal Azure. Para saber mais, consulte o artigo sobre a entrada de [provisões em contentores e bases](set-throughput.md)de dados . Este artigo explica os benefícios relativos da fixação de entrada em qualquer base de [dados](set-throughput.md#set-throughput-on-a-database) ou ao nível do [contentor](set-throughput.md#set-throughput-on-a-container) no portal Azure. Os termos "base de dados" e "contentor" mencionados nestes artigos mapeiam para "keyspace" e "table" respectivamente para a API cassandra.

A vantagem deste método é que é uma forma simples de gerir a capacidade de entrada na base de dados. No entanto, a desvantagem é que, em muitos casos, a sua abordagem à escala ção pode exigir que certos níveis de automatização sejam simultaneamente rentáveis e de alto desempenho. As secções seguintes explicam os cenários e métodos relevantes.

## <a id="use-control-plane"></a>Use o plano de controlo

A API do Azure Cosmos DB para Cassandra fornece a capacidade de ajustar a entrada programáticamente utilizando as nossas várias funcionalidades de plano de controlo. Consulte os artigos [Azure Resource Manager,](manage-cassandra-with-resource-manager.md) [Powershell](powershell-samples-cassandra.md)e [Azure CLI](cli-samples-cassandra.md) para obter orientação e amostras.

A vantagem deste método é que pode automatizar a escala para cima ou para baixo de recursos com base num temporizador para responder à atividade máxima, ou períodos de baixa atividade. Veja [aqui](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) a nossa amostra para saber como o conseguir usando funções Azure e Powershell.

Uma desvantagem com esta abordagem pode ser o facto de não poder responder às necessidades imprevisíveis de mudança de escala em tempo real. Em vez disso, poderá ter de alavancar o contexto de aplicação no seu sistema, ao nível cliente/SDK, ou utilizar o [Autopilot](provision-throughput-autopilot.md).

## <a id="use-cql-queries"></a>Utilize consultas CQL com um SDK específico

Pode escalar o sistema dinamicamente com código executando os [comandos CQL ALTER](cassandra-support.md#keyspace-and-table-options) para a base de dados ou contentor dada.

A vantagem desta abordagem é que permite responder às necessidades de escala de forma dinâmica e personalizada que se adequa à sua aplicação. Com esta abordagem, ainda pode alavancar as taxas e taxas padrão de RU/s. Se as necessidades de escala do seu sistema forem maioritariamente previsíveis (cerca de 70% ou mais), usar SDK com CQL pode ser um método mais rentável de escala automática do que usar o Autopilot. A desvantagem desta abordagem é que pode ser bastante complexo implementar repetições, enquanto a limitação das taxas pode aumentar a latência.

## <a id="use-autopilot"></a>Utilizar o Piloto Automático

Além da forma manual ou programática de fornecimento de entrada, também pode configurar recipientes de cosmos Azure no modo Autopilot. O modo piloto automático escalará automaticamente e instantaneamente as suas necessidades de consumo dentro das gamas DERU especificadas sem comprometer os SLAs. Para saber mais, consulte os recipientes e bases de [dados Create Azure Cosmos em artigo](provision-throughput-autopilot.md) de modo piloto automático.

A vantagem desta abordagem é que é a forma mais fácil de gerir as necessidades de escala no seu sistema. Garante não aplicar a limitação das taxas **dentro das gamas DERU configuradas**. A desvantagem é que, se as necessidades de escala no seu sistema forem previsíveis, o Autopilot pode ser uma forma menos rentável de lidar com as suas necessidades de escala do que utilizar o plano de controlo sob medida ou as abordagens de nível SDK acima mencionadas.

## <a name="next-steps"></a>Passos seguintes

- Comece por [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java
