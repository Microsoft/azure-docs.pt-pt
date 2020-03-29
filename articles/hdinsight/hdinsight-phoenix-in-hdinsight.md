---
title: Apache Phoenix em HDInsight - Azure HDInsight
description: Visão geral de Apache Phoenix
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435500"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix em Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) é uma camada de base de dados relacional massivamente paralela construída em [Apache HBase](hbase/apache-hbase-overview.md). A Phoenix permite-lhe usar consultas semelhantes a SQL sobre HBase. A Phoenix utiliza os condutores JDBC por baixo para permitir que os utilizadores criem, eliminem, alterem tabelas SQL, índices, vistas e sequências, e linhas de subida individual e a granel. Phoenix usa compilação nativa noSQL em vez de usar mapReduce para compilar consultas, permitindo a criação de aplicações de baixa latência em cima de HBase. A Phoenix adiciona coprocessadores para suportar o código fornecido pelo cliente no espaço de endereço do servidor, executando o código co-localizado com os dados. Esta abordagem minimiza a transferência de dados cliente/servidor.

A Apache Phoenix abre grandes consultas de dados a não desenvolvedores que podem usar uma sintaxe semelhante a SQL em vez de programação. A Phoenix está altamente otimizada para hBase, ao contrário de outras ferramentas como [Apache Hive](hadoop/hdinsight-use-hive.md) e Apache Spark SQL. O benefício para os desenvolvedores é escrever consultas altamente performantes com muito menos código.

Quando submete uma consulta SQL, a Phoenix compila a consulta às chamadas nativas da HBase e executa a varredura (ou plano) em paralelo para otimização. Esta camada de abstração liberta o desenvolvedor de escrever mapReduce jobs, para se concentrar na lógica do negócio e no fluxo de trabalho da sua aplicação em torno do armazenamento de big data da Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Otimização de desempenho de consulta e outras funcionalidades

Apache Phoenix adiciona várias melhorias de desempenho e funcionalidades às consultas de HBase.

### <a name="secondary-indexes"></a>Índices secundários

HBase tem um único índice que é lexicograficamente classificado na chave da linha primária. Estes registos só podem ser acedidos através da chave da linha. O acesso aos registos através de qualquer coluna que não seja a chave da linha requer a digitalização de todos os dados enquanto aplica o filtro necessário. Num índice secundário, as colunas ou expressões indexadas formam uma chave de linha alternativa, permitindo olhares e análises de alcance nesse índice.

Crie um índice `CREATE INDEX` secundário com o comando:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Esta abordagem pode gerar um aumento significativo de desempenho em relação à execução de consultas indexadas. Este tipo de índice secundário é um índice de **cobertura,** contendo todas as colunas incluídas na consulta. Portanto, a procura de tabela não é necessária e o índice satisfaz toda a consulta.

### <a name="views"></a>Vistas

As vistas da Phoenix fornecem uma forma de ultrapassar uma limitação de HBase, onde o desempenho começa a degradar-se quando se cria mais de 100 tabelas físicas. As vistas da Phoenix permitem que *várias tabelas virtuais* partilhem uma tabela hbase física subjacente.

Criar uma vista Phoenix é semelhante ao uso de sintaxe de vista SQL padrão. Uma diferença é que pode definir colunas para a sua visão, além das colunas herdadas da sua tabela base. Também pode adicionar `KeyValue` novas colunas.

Por exemplo, aqui está `product_metrics` uma tabela física com a seguinte definição:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Defina uma vista sobre esta tabela, com colunas adicionais:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Para adicionar mais colunas `ALTER VIEW` mais tarde, use a declaração.

### <a name="skip-scan"></a>Skip scan

Skip scan usa uma ou mais colunas de um índice composto para encontrar valores distintos. Ao contrário de uma varredura de gama, a varredura de salto implementa a digitalização intra-row, produzindo [um desempenho melhorado](https://phoenix.apache.org/performance.html#Skip-Scan). Durante a digitalização, o primeiro valor combinado é ignorado juntamente com o índice até que o valor seguinte seja encontrado.

Uma varredura de `SEEK_NEXT_USING_HINT` salto utiliza a enumeração do filtro HBase. Utilização, `SEEK_NEXT_USING_HINT`a varredura de salto mantém-se a ver com que conjunto de teclas, ou intervalos de teclas, estão a ser procurados em cada coluna. O skip scan pega então numa chave que lhe foi passada durante a avaliação do filtro, e determina se é uma das combinações. Caso contrário, a varredura de salto avalia a próxima chave mais alta para saltar.

### <a name="transactions"></a>Transações

Enquanto a HBase fornece transações ao nível da linha, a Phoenix integra-se com a [Tephra](https://tephra.io/) para adicionar suporte de transações transversais e transversais com semântica [ACID](https://en.wikipedia.org/wiki/ACID) completa.

Tal como acontece com as transações tradicionais da SQL, as transações fornecidas através do gestor de transações Phoenix permitem-lhe garantir que uma unidade atómica de dados seja inserida com sucesso, revertendo a transação se a operação de upsert falhar em qualquer tabela ativada por transações.

Para permitir transações da Phoenix, consulte a documentação da [transação Apache Phoenix.](https://phoenix.apache.org/transactions.html)

Para criar uma nova tabela com transações `true` ativadas, deteteto o `CREATE` `TRANSACTIONAL` imóvel em comunicado:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Para alterar uma tabela existente para ser transacional, utilize a mesma propriedade em comunicado: `ALTER`

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Não pode mudar uma tabela transacional de volta para não transacional.

### <a name="salted-tables"></a>Mesas salgadas

*O hotspoting* do servidor da região pode ocorrer ao escrever registos com chaves sequenciais para HBase. Embora possa ter vários servidores da região no seu cluster, as suas escritas estão todas a ocorrer apenas num. Esta concentração cria o problema de hotspoting onde, em vez da sua carga de trabalho escrita ser distribuída por todos os servidores da região disponíveis, apenas um está a lidar com a carga. Uma vez que cada região tem um tamanho máximo predefinido, quando uma região atinge esse limite de tamanho, é dividida em duas pequenas regiões. Quando isso acontece, uma destas novas regiões leva todos os novos recordes, tornando-se o novo hotspot.

Para mitigar este problema e alcançar um melhor desempenho, as tabelas pré-divididas para que todos os servidores da região sejam igualmente utilizados. Phoenix fornece *mesas salgadas,* adicionando transparentemente o byte de salga à chave da linha para uma determinada mesa. A tabela é pré-dividida nos limites do byte de sal para garantir a distribuição de carga igual entre os servidores da região durante a fase inicial da tabela. Esta abordagem distribui a carga de trabalho de escrita em todos os servidores da região disponíveis, melhorando a escrita e leia o desempenho. Para salgar uma `SALT_BUCKETS` mesa, especifique a propriedade da mesa quando a mesa for criada:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Ativar e sintonizar Phoenix com Apache Ambari

Um cluster HDInsight HBase inclui o [UI Ambari](hdinsight-hadoop-manage-ambari.md) para fazer alterações de configuração.

1. Para ativar ou desativar a Phoenix e controlar as definições de tempo de`https://YOUR_CLUSTER_NAME.azurehdinsight.net`tempo de consulta da Phoenix, inicie sessão no Ambari Web UI () utilizando as credenciais de utilizador hadoop.

2. Selecione **HBase** da lista de serviços no menu à esquerda e, em seguida, selecione o separador **Configs.**

    ![Configurações Apache Ambari HBase](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Encontre a secção de configuração **Phoenix SQL** para ativar ou desativar a fénix e detetete o tempo de consulta.

    ![Secção de configuração SQL Ambari Phoenix](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Consulte também

* [Use Apache Phoenix com clusters HBase baseados em Linux em HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Use Apache Zeppelin para executar consultas Apache Phoenix sobre Apache HBase em Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
