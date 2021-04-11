---
title: Resolução de problemas erros comuns na Azure Cosmos DB Cassandra API
description: Este artigo discute questões comuns na Azure Cosmos DB Cassandra API e como resolvê-las.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967358"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Resolução de problemas problemas comuns na Azure Cosmos DB Cassandra API

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Cassandra API em [Azure Cosmos DB](./introduction.md) é uma camada de compatibilidade que fornece [suporte de protocolo de fio](cassandra-support.md) para a base de dados Apache Cassandra de código aberto.

Este artigo descreve erros e soluções comuns para aplicações que utilizam a Azure Cosmos DB Cassandra API. Se o seu erro não estiver listado e tiver um erro ao executar uma [operação apoiada em Cassandra,](cassandra-support.md)mas o erro não está presente quando utilizar apaches nativas, [crie um pedido de suporte Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Como um serviço totalmente gerido na nuvem, a Azure Cosmos DB oferece [garantias sobre disponibilidade, produção e consistência](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) para a API cassandra. A API cassandra também facilita as operações da plataforma de manutenção zero e o remendos de tempo zero.
>
>Estas garantias não são possíveis em implementações anteriores da Apache Cassandra, pelo que muitas das operações de back-end da Cassandra API diferem da Apache Cassandra. Recomendamos configurações e abordagens específicas para ajudar a evitar erros comuns.

## <a name="nonodeavailableexception"></a>NoNodeAvailableExcepção

Este erro é uma exceção de invólucro de alto nível com um grande número de possíveis causas e exceções internas, muitas das quais podem estar relacionadas com o cliente.

Causas e soluções comuns:

- **Intervalo de tempo inativo da Azure LoadBalancers**: Esta questão também pode manifestar-se como `ClosedConnectionException` . Para resolver o problema, defina a definição de manter-se viva no condutor (ver [Ativar a vida para o condutor Java)](#enable-keep-alive-for-the-java-driver)e aumente as definições de manter-se vivas no seu sistema operativo ou ajuste o [tempo limite de marcha lenta em Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Esgotamento do recurso de aplicação do cliente**: Certifique-se de que as máquinas clientes dispõem de recursos suficientes para completar o pedido.

## <a name="cant-connect-to-a-host"></a>Não se liga a um hospedeiro.

Pode ver este erro: "Não é possível ligar-se a nenhum hospedeiro, agendando novamente em 600000 milissegundos."

Este erro pode ser causado pela exaustão da tradução de endereços de rede de origem (SNAT) do lado do cliente. Siga os passos no [SNAT para que as ligações de saída](../load-balancer/load-balancer-outbound-connections.md) excluam esta questão.

O erro também pode ser um problema de tempo de tempo inativo onde o equilibrador de carga Azure tem quatro minutos de tempo de inatividade por defeito. Consulte [o tempo limite do balançador de carga](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Ativar a vida para o condutor java](#enable-keep-alive-for-the-java-driver) e definir o intervalo no sistema operativo em menos de quatro `keepAlive` minutos.

## <a name="overloadedexception-java"></a>SobrecarregadaExcepção (Java)

Os pedidos são acelerados porque o número total de unidades de pedido consumidas é superior ao número de unidades de pedido que a provisionou no espaço-chave ou na tabela.

Considere escalar a produção atribuída a um espaço-chave ou tabela a partir do portal Azure (ver [elasticamente escalar uma conta AZure Cosmos DB Cassandra API)](manage-scale-cassandra.md)ou implementar uma política de retrip.

Para Java, consulte as amostras de repetição para o [condutor v3.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e para o [condutor v4.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Consulte também [as extensões de Azure Cosmos Cassandra para Java.](https://github.com/Azure/azure-cosmos-cassandra-extensions)

### <a name="overloadedexception-despite-sufficient-throughput"></a>SobrecargaExcepção apesar da produção suficiente

O sistema parece estar a estrangular os pedidos, embora seja previsto um rendimento suficiente para o volume de pedido ou para o custo unitário do pedido consumido. Há duas causas possíveis:

- **Operações de nível de esquema**: A API Cassandra implementa um orçamento de produção do sistema para operações de nível de esquema (CREATE TABLE, ALTER TABLE, DROP TABLE). Este orçamento deve ser suficiente para as operações de esquema num sistema produtivo. No entanto, se tiver um elevado número de operações de nível de esquema, poderá ultrapassar este limite.

  Como o orçamento não é controlado pelo utilizador, considere baixar o número de operações de esquema que executou. Se essa ação não resolver o problema ou não for viável para a sua carga de trabalho, [crie um pedido de apoio ao Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Distorção de dados**: Quando a produção é adcêida na API de Cassandra, é dividida igualmente entre divisórias físicas, e cada divisória física tem um limite superior. Se tiver uma elevada quantidade de dados sendo inseridos ou consultados a partir de uma partição particular, pode ser limitado pela taxa, mesmo que forneça uma grande quantidade de produção global (unidades de pedido) para esse quadro.

  Reveja o seu modelo de dados e certifique-se de que não tem distorções excessivas que possam causar divisórias quentes.

## <a name="intermittent-connectivity-errors-java"></a>Erros de conectividade intermitentes (Java)

A ligação cai ou os tempos para fora inesperadamente.

Os pilotos apache cassandra para Java fornecem duas políticas de reconexão nativas: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . A predefinição é `ExponentialReconnectionPolicy`. No entanto, para a Azure Cosmos DB Cassandra API, recomendamos `ConstantReconnectionPolicy` com um atraso de dois segundos.

Consulte a [documentação para o controlador Java 4.x,](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)a [documentação para o condutor Java 3.x](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)ou [Configuração De ReconexãoPolicy para os](#configure-reconnectionpolicy-for-the-java-driver) exemplos do condutor Java.

## <a name="error-with-load-balancing-policy"></a>Erro com política de equilíbrio de carga

Pode ter implementado uma política de equilíbrio de carga em v3.x do controlador Java DataStax, com código semelhante a:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Se o valor `withLocalDc()` for não corresponder ao datacenter do ponto de contacto, poderá sofrer um erro intermitente: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

Implementar a [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Para que funcione, poderá ser necessário atualizar o DataStax utilizando o seguinte código:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>A contagem falha numa mesa grande.

Quando corre `select count(*) from table` ou é semelhante para um grande número de linhas, o servidor esgota-se.

Se estiver a utilizar um cliente CQLSH local, altere as `--connect-timeout` definições ou `--request-timeout` as definições. Ver [cqlsh: a concha CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Se a contagem ainda esgotar, pode obter uma contagem de registos da telemetria traseira Azure Cosmos DB, indo ao separador métricas do portal Azure, selecionando a métrica `document count` , e adicionando um filtro para a base de dados ou recolha (o analógico da tabela em Azure Cosmos DB). Em seguida, pode pairar sobre o gráfico resultante para o ponto no tempo em que deseja uma contagem do número de registos.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="métricas vista":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Configurar Reconexão Política para o condutor de Java

### <a name="version-3x"></a>Versão 3.x

Para a versão 3.x do controlador Java, configuure a política de reconexão quando criar um objeto de cluster:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versão 4.x

Para a versão 4.x do controlador Java, configurar a política de reconexão através de definições dominantes no `reference.conf` ficheiro:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Habilitar manter-se vivo para o piloto de Java

### <a name="version-3x"></a>Versão 3.x

Para a versão 3.x do controlador Java, desaperte-se de manter vivo quando criar um objeto de cluster e, em seguida, certifique-se de que a vida está [ativada no sistema operativo:](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Versão 4.x

Para a versão 4.x do controlador Java, defina manter-se vivo através de definições dominantes em `reference.conf` , e, em seguida, certifique-se de que a vida está [ativada no sistema operativo](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Conheça [as funcionalidades suportadas](cassandra-support.md) na Azure Cosmos DB Cassandra API.
- Saiba como [migrar da apache nativa Cassandra para Azure Cosmos DB Cassandra API](cassandra-migrate-cosmos-db-databricks.md).
