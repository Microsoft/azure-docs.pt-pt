---
title: Apache Phoenix no HDInsight - Azure HDInsight
description: ''
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 7d9aafeb920eab7f6a87061a135bf2e464add436
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697999"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix no HDInsight

[O Apache Phoenix](https://phoenix.apache.org/) é uma código-fonte aberto, camada de base de dados relacional paralelo em massa criada na [Apache HBase](hbase/apache-hbase-overview.md). Phoenix permite-lhe utilizar consultas do tipo SQL através de HBase. Phoenix utiliza controladores JDBC por baixo, para permitir que os utilizadores a criar, eliminar, alter tabelas SQL, índices, Exibições e sequências e linhas de upsert individualmente e em massa. Phoenix usa a compilação nativo do noSQL, em vez de utilizar o MapReduce para compilar consultas, permitindo a criação de aplicativos de baixa latência sobre o HBase. Phoenix adiciona coprocessors para suportar a execução do código fornecido pelo cliente no espaço de endereços de servidor, executando o código na mesma localização os dados. Essa abordagem minimiza a transferência de dados de cliente/servidor.

O Apache Phoenix abre-se as consultas de macrodados para não-desenvolvedores que podem utilizar uma sintaxe semelhante a SQL em vez de programação. Phoenix está altamente otimizado para o HBase, ao contrário de outras ferramentas, tal como [Apache Hive](hadoop/hdinsight-use-hive.md) e Apache Spark SQL. A vantagem para os desenvolvedores, é altamente escrever consultas de alto desempenho com muito menos código.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Quando submete uma consulta SQL, o Phoenix compila a consulta para chamadas nativas do HBase e executa a análise (ou o plano) em paralelo para a otimização. Essa camada de abstração libera o desenvolvedor da escrita de tarefas de MapReduce, para se concentrar em vez disso, a lógica de negócios e o fluxo de trabalho de seu aplicativo em todo o armazenamento de grandes volumes de dados do Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Otimização do desempenho de consulta e outros recursos

O Apache Phoenix adiciona vários melhoramentos de desempenho e recursos a consultas de HBase.

### <a name="secondary-indexes"></a>Índices secundários

HBase tem um único índice é lexicographically classificado pela chave de linha principal. Estes registos só podem ser acedidos através da chave de linha. Aceder a registos através de qualquer coluna que não seja a chave de linha requer a análise de todos os dados ao aplicar o filtro necessário. Um índice secundário, as colunas ou expressões que são indexados de forma uma chave de linha alternativo, permitindo que pesquisas e o intervalo de analisa desse índice.

Criar um índice secundário com o `CREATE INDEX` comando:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Essa abordagem pode resultar num aumento significativo no desempenho ao longo da execução de consultas único indexados. Este tipo de índice secundário é uma **índice abrangente**, que contém todas as colunas incluídas na consulta. Por conseguinte, a pesquisa da tabela não é necessária e o índice satisfaça a consulta inteira.

### <a name="views"></a>Vistas

Vistas de Phoenix fornecem uma forma de superar uma limitação de HBase, onde o desempenho começa a cair ao criar mais do que cerca de 100 tabelas físicas. Vistas de Phoenix ativar vários *tabelas virtuais* para partilhar uma tabela de HBase física subjacente.

Criar uma vista de Phoenix é semelhante à utilização da sintaxe de vista SQL padrão. Uma diferença é que pode definir o colunas para a vista, além das colunas herdadas da sua tabela base. Também pode adicionar novos `KeyValue` colunas.

Por exemplo, aqui está uma tabela física com o nome `product_metrics` com a seguinte definição:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Defina uma vista de sobre essa tabela, com colunas adicionais:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Para adicionar mais colunas mais tarde, utilize o `ALTER VIEW` instrução.

### <a name="skip-scan"></a>Análise de ignorar

Análise de ignorar usa uma ou mais colunas de um índice composto para encontrar valores distintos. Ao contrário de uma verificação de intervalo de análise de ignorar implementa intra-linhas análise, gerando [melhorou o desempenho](https://phoenix.apache.org/performance.html#Skip-Scan). Durante a verificação, o primeiro valor correspondente é ignorado, juntamente com o índice até encontra o valor seguinte.

Utiliza uma análise de ignorar o `SEEK_NEXT_USING_HINT` enumeração do filtro de HBase. Usando `SEEK_NEXT_USING_HINT`, a análise de ignorar mantém um registro de qual conjunto de chaves, ou intervalos de chaves, estão a ser procurado para cada coluna. A ignorar de análise, em seguida, usa uma chave que foi passada para ele durante a avaliação de filtro e determina se é uma das combinações. Caso contrário, a análise de ignorar avalia a seguinte chave de mais alta para saltar para.

### <a name="transactions"></a>Transações

Enquanto o HBase fornece ao nível da linha de transações, Phoenix integra [Tephra](https://tephra.io/) para adicionar suporte a transações da linha e entre tabelas com completo [ACID](https://en.wikipedia.org/wiki/ACID) semântica.

Como com transações tradicionais de SQL, transações fornecidas através do Gestor de transações de Phoenix permitem-lhe Certifique-se de que uma unidade atômica de dados é com êxito upserted, reverter a transação se a operação de upsert falhar em qualquer tabela habilitados na transação.

Para permitir transações de Phoenix, consulte a [documentação de transação de Apache Phoenix](https://phoenix.apache.org/transactions.html).

Para criar uma nova tabela com transações ativadas, defina o `TRANSACTIONAL` propriedade `true` num `CREATE` instrução:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Para alterar uma tabela existente seja transacional, utilize a mesma propriedade num `ALTER` instrução:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Não é possível voltar a mudar uma tabela transacional para a ser não transacional.

### <a name="salted-tables"></a>Tabelas de Salt

*Hotspotting de servidor de região* pode ocorrer durante a escrita de registos com chaves sequenciais para HBase. Embora pode ter vários servidores de região no seu cluster, as escritas são todos que ocorrem em apenas um. Essa concentração cria o problema de hotspotting onde, em vez de sua carga de trabalho de escrita, sendo distribuída entre todos os servidores de região disponível, apenas um está a processar a carga. Uma vez que cada região tem um tamanho máximo predefinido, quando uma região atinge esse limite de tamanho, ele é dividido em duas regiões pequenas. Quando isso acontece, um destas novas regiões leva todos os novos registros, tornando-se o ponto de acesso de novo.

Para mitigar este problema e melhorar o desempenho, divida previamente tabelas para que todos os servidores de região são igualmente utilizados. Fornece a Phoenix *tabelas com SALT*, de forma transparente a adicionar o byte de inclusão de Salt para a chave de linha para uma determinada tabela. A tabela é previamente dividir sobre os limites de byte de salt para garantir que a distribuição de carga igual entre servidores de região durante a fase inicial da tabela. Essa abordagem distribui a carga de trabalho de escrita por todos os servidores de região disponível, melhorando a gravação e desempenho de leitura. Para salt uma tabela, especifique o `SALT_BUCKETS` propriedade de tabela quando a tabela é criada:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Ativar e otimizar o Phoenix com Apache Ambari

Um cluster do HBase do HDInsight inclui a [IU do Ambari](hdinsight-hadoop-manage-ambari.md) para alterações de configuração.

1. Para ativar ou desativar a Phoenix e para controlar as definições de tempo limite de consulta do Phoenix, inicie sessão na IU da Web do Ambari (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) com as suas credenciais de utilizador do Hadoop.

2. Selecione **HBase** na lista de serviços no menu da esquerda, em seguida, selecione a **configurações** separador.

    ![Ambari HBase config](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Encontrar o **Phoenix SQL** seção de configuração para ativar ou desativar a phoenix e definir o tempo limite de consulta.

    ![Seção de configuração do SQL do Phoenix de Ambari](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Consulte também

* [Utilizar o Apache Phoenix com clusters de HBase baseado em Linux no HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
