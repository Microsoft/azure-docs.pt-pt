---
title: Apache Phoenix no HDInsight – Azure HDInsight
description: Visão geral do Apache Phoenix
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435500"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix no Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) é uma camada de banco de dados relacional de software livre, massivamente paralela, criada no [Apache HBase](hbase/apache-hbase-overview.md). O Phoenix permite que você use consultas do tipo SQL no HBase. O Phoenix usa drivers JDBC abaixo para permitir que os usuários criem, excluam, alterem tabelas SQL, índices, exibições e sequências e Upsert linhas individualmente e em massa. O Phoenix usa a compilação nativa noSQL em vez de usar o MapReduce para compilar consultas, permitindo a criação de aplicativos de baixa latência sobre o HBase. O Phoenix adiciona coprocessadores para dar suporte à execução de código fornecido pelo cliente no espaço de endereço do servidor, executando o código colocado com os dados. Essa abordagem minimiza a transferência de dados do cliente/servidor.

Apache Phoenix abre Big Data consultas a não-desenvolvedores que podem usar uma sintaxe do tipo SQL em vez de programação. O Phoenix é altamente otimizado para o HBase, ao contrário de outras ferramentas, como [Apache Hive](hadoop/hdinsight-use-hive.md) e Apache Spark SQL. O benefício para os desenvolvedores é escrever consultas altamente de alto desempenho com muito menos código.

Quando você envia uma consulta SQL, o Phoenix compila a consulta para chamadas nativas do HBase e executa a verificação (ou o plano) em paralelo para otimização. Essa camada de abstração libera o desenvolvedor de escrever trabalhos MapReduce, para se concentrar na lógica de negócios e no fluxo de trabalho de seus aplicativos em relação ao armazenamento Big Data da Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Otimização de desempenho de consultas e outros recursos

Apache Phoenix adiciona vários recursos e aprimoramentos de desempenho a consultas do HBase.

### <a name="secondary-indexes"></a>Índices secundários

O HBase tem um único índice que é modo lexicográfico classificado na chave de linha primária. Esses registros só podem ser acessados por meio da chave de linha. O acesso a registros por meio de qualquer coluna que não seja a chave de linha requer a verificação de todos os dados ao aplicar o filtro necessário. Em um índice secundário, as colunas ou expressões que são indexadas formam uma chave de linha alternativa, permitindo pesquisas e verificações de intervalo nesse índice.

Crie um índice secundário com o comando `CREATE INDEX`:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Essa abordagem pode gerar um aumento significativo de desempenho em relação à execução de consultas de índices únicos. Esse tipo de índice secundário é um **índice abrangente**, contendo todas as colunas incluídas na consulta. Portanto, a pesquisa de tabela não é necessária e o índice satisfaz a consulta inteira.

### <a name="views"></a>Vistas

As exibições de Phoenix fornecem uma maneira de superar uma limitação do HBase, em que o desempenho começa a ser prejudicado quando você cria mais de cerca de 100 tabelas físicas. As exibições do Phoenix permitem que várias *tabelas virtuais* compartilhem uma tabela física do HBase subjacente.

A criação de uma exibição Phoenix é semelhante ao uso da sintaxe de exibição SQL padrão. Uma diferença é que você pode definir colunas para sua exibição, além das colunas herdadas de sua tabela base. Você também pode adicionar novas colunas de `KeyValue`.

Por exemplo, aqui está uma tabela física chamada `product_metrics` com a seguinte definição:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Defina uma exibição nessa tabela com colunas adicionais:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Para adicionar mais colunas posteriormente, use a instrução `ALTER VIEW`.

### <a name="skip-scan"></a>Ignorar verificação

Ignorar verificação usa uma ou mais colunas de um índice composto para localizar valores distintos. Ao contrário de uma verificação de intervalo, a verificação de ignorar implementa a verificação dentro da linha, produzindo um [desempenho aprimorado](https://phoenix.apache.org/performance.html#Skip-Scan). Durante a verificação, o primeiro valor correspondente é ignorado junto com o índice até que o próximo valor seja encontrado.

Uma verificação de ignorar usa a enumeração `SEEK_NEXT_USING_HINT` do filtro HBase. Usando `SEEK_NEXT_USING_HINT`, a verificação de ignorar mantém o controle de qual conjunto de chaves, ou intervalos de chaves, está sendo pesquisado em cada coluna. Em seguida, a verificação de ignorar usa uma chave que foi passada durante a avaliação do filtro e determina se ela é uma das combinações. Caso contrário, a verificação de ignorar avaliará a próxima chave mais alta para saltar.

### <a name="transactions"></a>Transações

Embora o HBase forneça transações de nível de linha, o Phoenix se integra com [tephra](https://tephra.io/) para adicionar suporte a transações entre linhas e entre tabelas com semântica [Acid](https://en.wikipedia.org/wiki/ACID) completa.

Assim como acontece com transações SQL tradicionais, as transações fornecidas por meio do Gerenciador de transações Phoenix permitem garantir que uma unidade atômica de dados seja introduzida com êxito, revertendo a transação se a operação Upsert falhar em qualquer tabela habilitada para transação.

Para habilitar transações Phoenix, consulte a [documentação da transação Apache Phoenix](https://phoenix.apache.org/transactions.html).

Para criar uma nova tabela com transações habilitadas, defina a propriedade `TRANSACTIONAL` como `true` em uma instrução `CREATE`:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Para alterar uma tabela existente para ser transacional, use a mesma propriedade em uma instrução `ALTER`:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Não é possível mudar uma tabela transacional de volta para ser não transacional.

### <a name="salted-tables"></a>Tabelas com Salt

O *hotspotting do servidor de região* pode ocorrer ao gravar registros com chaves sequenciais no HBase. Embora você possa ter vários servidores de região em seu cluster, suas gravações estão ocorrendo em apenas um. Essa concentração cria o problema de hotspotting em que, em vez de sua carga de trabalho de gravação sendo distribuída entre todos os servidores de região disponíveis, apenas um está lidando com a carga. Como cada região tem um tamanho máximo predefinido, quando uma região atinge esse limite de tamanho, ela é dividida em duas regiões pequenas. Quando isso acontece, uma dessas novas regiões usa todos os novos registros, tornando-se o novo hotspot.

Para atenuar esse problema e obter melhor desempenho, divida tabelas de forma que todos os servidores de região sejam usados igualmente. Phoenix fornece *tabelas salted*, adicionando de forma transparente o byte de Salting à chave de linha de uma tabela específica. A tabela é previamente dividida nos limites de bytes de Salt para garantir a distribuição de carga igual entre os servidores de região durante a fase inicial da tabela. Essa abordagem distribui a carga de trabalho de gravação em todos os servidores de região disponíveis, melhorando o desempenho de gravação e leitura. Para Salt de uma tabela, especifique a propriedade da tabela de `SALT_BUCKETS` quando a tabela for criada:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Habilitar e ajustar a Phoenix com o Apache Ambari

HDInsight An cluster HBase inclui a [interface do usuário do amAmbari](hdinsight-hadoop-manage-ambari.md) para fazer alterações de configuração.

1. Para habilitar ou desabilitar o Phoenix e controlar as configurações de tempo limite de consulta da Phoenix, faça logon na interface do usuário da Web do amAmbari (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) usando suas credenciais de usuários do Hadoop.

2. Selecione **HBase** na lista de serviços no menu esquerdo e, em seguida, selecione a guia **configurações** .

    ![Configurações do Apache Ambari HBase](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Localize a seção de configuração do **Phoenix SQL** para habilitar ou desabilitar o Phoenix e defina o tempo limite da consulta.

    ![Seção de configuração do SQL Ambari Phoenix](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Ver também

* [Usar Apache Phoenix com clusters HBase baseados em Linux no HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Usar o Apache Zeppelin para executar consultas de Apache Phoenix sobre o Apache HBase no Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
