---
title: Resolução de problemas erros comuns em Azure Cosmos DB Cassandra API
description: Este médico discute as formas de resolver problemas comuns encontrados na Azure Cosmos DB Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 6d9a74729768a326379b5efddb864a4fee02fa59
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493226"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Resolução de problemas problemas comuns na Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API em Azure Cosmos DB é uma camada de compatibilidade, que fornece [suporte de protocolo de fio](cassandra-support.md) para a popular base de dados Apache Cassandra de código aberto, e é alimentada por [Azure Cosmos DB](./introduction.md). Como um serviço totalmente gerido na nuvem, a Azure Cosmos DB oferece [garantias sobre disponibilidade, produção e consistência](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) para a API de Cassandra. Estas garantias não são possíveis nas implementações do legado de Apache Cassandra. A API cassandra também facilita as operações da plataforma de manutenção zero e o remendos de tempo zero. Como tal, muitas das suas operações de backend são diferentes da Apache Cassandra, por isso recomendamos configurações e abordagens específicas para evitar erros comuns. 

Este artigo descreve erros e soluções comuns para aplicações que consomem Azure Cosmos DB Cassandra API. Se o seu erro não estiver listado abaixo, e estiver a sofrer um erro ao executar uma [operação apoiada na API Cassandra,](cassandra-support.md)onde o erro não está *presente ao utilizar a Apache Cassandra nativa,* crie um pedido de apoio [Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableExcepção
Esta é uma exceção de invólucro de alto nível com um grande número de possíveis causas e exceções internas, muitas das quais podem estar relacionadas com o cliente. 
### <a name="solution"></a>Solução
Algumas causas e soluções populares são as seguintes: 
- Intervalo de tempo inativo da Azure LoadBalancers: Isto também pode manifestar-se como `ClosedConnectionException` . Para resolver isto, defina a definição viva no condutor (ver [abaixo)](#enable-keep-alive-for-java-driver)e aumente as definições de manter-se vivas no sistema operativo ou [ajuste o tempo limite de marcha lenta em Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Esgotamento do recurso de aplicação do cliente:** certifique-se de que as máquinas clientes têm recursos suficientes para completar o pedido. 

## <a name="cannot-connect-to-host"></a>Não é possível ligar-se ao anfitrião
Pode ver este erro: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Solução
Isto pode ser exaustão do SNAT do lado do cliente. Por favor, siga os passos no [SNAT para que as ligações de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) excluam esta questão. Isto também pode ser um problema de tempo de ciosidade em que o equilibrador de carga Azure tem 4 minutos de tempo de inatividade por defeito. Consulte a documentação no [tempo limite de marcha lenta .](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal) Ativar a televisão a manter-se viva a partir das definições do condutor (ver [abaixo)](#enable-keep-alive-for-java-driver)e definir `keepAlive` o intervalo no sistema operativo para menos de 4 minutos.

 

## <a name="overloadedexception-java"></a>SobrecarregadaExcepção (Java)
O número total de unidades de pedido consumidas é superior às unidades de pedido previstas no espaço-chave ou na tabela. Os pedidos estão acelerados.
### <a name="solution"></a>Solução
Considere escalar a produção atribuída a um espaço-chave ou tabela a partir do portal Azure (consulte [aqui](manage-scale-cassandra.md) para operações de escala na API de Cassandra) ou pode implementar uma política de reação. Para Java, consulte amostras de repetição para [o condutor v3.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [o condutor v4.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Consulte também [as extensões de Azure Cosmos Cassandra para Java.](https://github.com/Azure/azure-cosmos-cassandra-extensions)

### <a name="overloadedexception-even-with-sufficient-throughput"></a>SobrecargaExcepção mesmo com produção suficiente 
O sistema parece estar a anular os pedidos, apesar de ser prevista uma produção suficiente para o volume de pedido e/ou para o custo unitário do pedido consumido. Existem duas possíveis causas de limitação inesperada da taxa:
- **Operações de nível de esquema:** A API Cassandra implementa um orçamento de produção do sistema para operações de nível de esquema (CREATE TABLE, ALTER TABLE, DROP TABLE). Este orçamento deve ser suficiente para as operações de esquema num sistema produtivo. No entanto, se tiver um elevado número de operações de nível de esquema, é possível que esteja a ultrapassar este limite. Uma vez que este orçamento não é controlado pelo utilizador, terá de considerar a redução do número de operações de esquema que estão a ser executadas. Se tomar esta medida não resolver o problema, ou se não for viável para a sua carga de trabalho, [crie um pedido de apoio ao Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Distorção de dados:** quando a produção é aquisitada na API de Cassandra, é dividida igualmente entre divisórias físicas, e cada partição física tem um limite superior. Se tiver uma elevada quantidade de dados sendo inseridos ou consultados a partir de uma partição particular, é possível ser limitado a taxa, apesar de prever uma grande quantidade de produção global (unidades de pedido) para esse quadro. Reveja o seu modelo de dados e certifique-se de que não tem distorções excessivas que possam estar a causar divisórias quentes. 

## <a name="intermittent-connectivity-errors-java"></a>Erros de conectividade intermitentes (Java) 
A ligação cai ou os tempos para fora inesperadamente.

### <a name="solution"></a>Solução 
Os pilotos apache cassandra para Java fornecem duas políticas de reconexão nativas: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . A predefinição é `ExponentialReconnectionPolicy`. No entanto, para a Azure Cosmos DB Cassandra API, recomendamos `ConstantReconnectionPolicy` com um atraso de 2 segundos. Consulte a [documentação](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  do condutor do java v4.x e [aqui](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) para orientação java 3.x consulte também [Configurar a ReconexãoPolicy para](#configuring-reconnectionpolicy-for-java-driver) exemplos de Java Driver abaixo.

## <a name="error-with-load-balancing-policy"></a>Erro com política de equilíbrio de carga

Se implementou uma política de equilíbrio de carga em v3.x do controlador Java Datastax, com código semelhante ao seguinte:

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

Se o valor for `withLocalDc()` não corresponder ao datacenter do ponto de contacto, poderá sofrer um erro muito intermitente: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Solução 
Implementar [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (pode ser necessário atualizar a versão menor do datastax para que funcione):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Contagem falha em mesa grande
Quando funciona `select count(*) from table` ou é semelhante para um grande número de linhas, o servidor esgota-se.

### <a name="solution"></a>Solução 
Se utilizar um cliente CQLSH local, pode tentar alterar as `--connect-timeout` definições ou `--request-timeout` configurações (consulte mais detalhes [aqui).](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) Se isso não for suficiente e contar ainda fora do tempo, pode obter uma contagem de registos da telemetria de backend Azure Cosmos, indo ao separador métricas no portal Azure, selecionando a `document count` métrica, adicionando um filtro para a base de dados ou recolha (o analógico da tabela em Azure Cosmos DB). Em seguida, pode pairar sobre o gráfico resultante para o ponto no tempo em que deseja uma contagem do número de registos.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="métricas vista":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Reconligação configurante Política para Java Driver

### <a name="version-3x"></a>Versão 3.x

Para a versão 3.x do controlador Java, configuure a política de reconexão ao criar um objeto de cluster:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versão 4.x

Para a versão 4.x do controlador Java, configurar a política de reconexão através da definição dominante no `reference.conf` ficheiro:

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

## <a name="enable-keep-alive-for-java-driver"></a>Ativar a vida para o Java Driver

### <a name="version-3x"></a>Versão 3.x

Para a versão 3.x do controlador Java, desista de manter-se vivo ao criar um objeto Cluster e certifique-se de que a sua vida está [ativada no sistema operativo:](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)

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

Para a versão 4.x do controlador Java, defina manter-se vivo através de definições dominantes `reference.conf` e certifique-se de que a sua vida está [ativada no sistema operativo:](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Conheça as [funcionalidades suportadas](cassandra-support.md) na Azure Cosmos DB Cassandra API.
- Saiba como [migrar da nativa Apache Cassandra para Azure Cosmos DB Cassandra API](cassandra-migrate-cosmos-db-databricks.md)