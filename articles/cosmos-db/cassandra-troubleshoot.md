---
title: Resolução de problemas erros comuns em Azure Cosmos DB Cassandra API
description: Este médico discute as formas de resolver problemas comuns encontrados na Azure Cosmos DB Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: c969e4fac3ae30088cfe47a7b0edff22c578cb8b
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802376"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Resolução de problemas problemas comuns na Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API em Azure Cosmos DB é uma camada de compatibilidade, que fornece [suporte de protocolo de fio](cassandra-support.md) para a popular base de dados Apache Cassandra de código aberto, e é alimentada por [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). Como um serviço totalmente gerido na nuvem, a Azure Cosmos DB oferece [garantias sobre disponibilidade, produção e consistência](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) para a API de Cassandra. Estas garantias não são possíveis nas implementações do legado de Apache Cassandra. A API cassandra também facilita as operações da plataforma de manutenção zero e o remendos de tempo zero. Como tal, muitas das suas operações de backend são diferentes da Apache Cassandra, por isso recomendamos configurações e abordagens específicas para evitar erros comuns. 

Este artigo descreve erros e soluções comuns para aplicações que consomem Azure Cosmos DB Cassandra API.

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

| Erro               |  Descrição             | Solução  |
|---------------------|--------------------------|-----------|
| SobrecarregadaExcepção (Java) | O número total de unidades de pedido consumidas é superior às unidades de pedido previstas no espaço-chave ou na tabela. Os pedidos estão acelerados. | Considere escalar a produção atribuída a um espaço-chave ou tabela a partir do portal Azure (consulte [aqui](manage-scale-cassandra.md) para operações de escala na API de Cassandra) ou pode implementar uma política de reação. Para Java, consulte amostras de repetição para [o condutor v3.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [o condutor v4.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Ver também [extensões de Azure Cosmos Cassandra para Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| SobrecargaExcepção (Java) mesmo com produção suficiente | O sistema parece estar a estrangular os pedidos, apesar de ser prevista uma produção suficiente para o volume de pedido e/ou o custo unitário do pedido consumido  | A API Cassandra implementa um orçamento de produção do sistema para operações de nível de esquema (CREATE TABLE, ALTER TABLE, DROP TABLE). Este orçamento deve ser suficiente para as operações de esquema num sistema produtivo. No entanto, se tiver um elevado número de operações de nível de esquema, é possível que esteja a ultrapassar este limite. Uma vez que este orçamento não é controlado pelo utilizador, terá de considerar a redução do número de operações de esquema que estão a ser executadas. Se tomar esta medida não resolver o problema, ou se não for viável para a sua carga de trabalho, [crie um pedido de apoio ao Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | Após um período de tempo inativo após ligações bem sucedidas, a aplicação é incapaz de se ligar| Este erro pode dever-se ao tempo limite de tempo inativo dos Azure LoadBalancers, que é de 4 minutos. Defina manter a definição viva no condutor (ver abaixo) e aumente as definições de manter-se vivos no sistema operativo ou [ajuste o tempo limite de marcha lenta em Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Outros erros de conectividade intermitentes (Java) | A ligação cai ou os tempos fora inesperadamente | Os pilotos apache cassandra para Java fornecem duas políticas de reconexão nativas: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . A predefinição é `ExponentialReconnectionPolicy`. No entanto, para a Azure Cosmos DB Cassandra API, recomendamos `ConstantReconnectionPolicy` com um atraso de 2 segundos. Consulte a [documentação](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  do condutor do java v4.x e [aqui](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) para orientação Java 3.x (consulte também os exemplos abaixo).|

Se o seu erro não estiver acima listado, e estiver a sofrer um erro ao executar uma [operação apoiada na API Cassandra,](cassandra-support.md)onde o erro não está *presente ao utilizar a Apache Cassandra nativa,* crie um pedido de apoio [Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)

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

