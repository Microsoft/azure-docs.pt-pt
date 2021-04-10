---
title: Apache Phoenix em HDInsight - Azure HDInsight
description: Visão geral de Apache Phoenix
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: cb5230ae42703d19726fb8ea0d6c88aa70e589a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864147"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix em Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) é uma camada de base de dados relacional massivamente paralela construída na [Apache HBase](hbase/apache-hbase-overview.md). A Phoenix permite-lhe utilizar consultas semelhantes a SQL sobre a HBase. A Phoenix utiliza controladores JDBC por baixo para permitir que os utilizadores criem, apaguem, alterem tabelas SQL, índices, vistas e sequências, e upsert rows individualmente e a granel. Phoenix usa a compilação nativa noSQL em vez de usar MapReduce para compilar consultas, permitindo a criação de aplicações de baixa latência em cima da HBase. A Phoenix adiciona coprocessadores para suportar o código fornecido pelo cliente no espaço de endereço do servidor, executando o código indicado com os dados. Esta abordagem minimiza a transferência de dados cliente/servidor.

Apache Phoenix abre grandes consultas de dados a não desenvolvedores que podem usar uma sintaxe semelhante ao SQL em vez de programação. Phoenix é altamente otimizado para HBase, ao contrário de outras ferramentas como [Apache Hive](hadoop/hdinsight-use-hive.md) e Apache Spark SQL. O benefício para os desenvolvedores é escrever consultas altamente performantes com muito menos código.

Quando submete uma consulta SQL, a Phoenix compila a consulta às chamadas nativas da HBase e executa a digitalização (ou plano) paralelamente à otimização. Esta camada de abstração liberta o desenvolvedor de escrever trabalhos mapReduce, para se concentrar na lógica de negócio e no fluxo de trabalho da sua aplicação em torno do grande armazenamento de dados da Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Otimização de desempenho de consulta e outras funcionalidades

Apache Phoenix adiciona várias melhorias de desempenho e funcionalidades a consultas HBase.

### <a name="secondary-indexes"></a>Índices secundários

HBase tem um único índice que é classificado lexicograficamente na tecla da linha primária. Estes registos só podem ser acedidos através da tecla da linha. Aceder aos registos através de qualquer coluna que não seja a tecla de linha requer a verificação de todos os dados enquanto aplica o filtro necessário. Num índice secundário, as colunas ou expressões indexadas formam uma chave de linha alternativa, permitindo análises e varreduras de alcance nesse índice.

Criar um índice secundário com o `CREATE INDEX` comando:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Esta abordagem pode gerar um aumento significativo de desempenho em relação à execução de consultas indexadas únicas. Este tipo de índice secundário é um **índice de cobertura,** contendo todas as colunas incluídas na consulta. Portanto, a procura da tabela não é necessária e o índice satisfaz toda a consulta.

### <a name="views"></a>Vistas

As vistas de Phoenix fornecem uma forma de superar uma limitação HBase, onde o desempenho começa a degradar-se quando cria mais de 100 tabelas físicas. As vistas phoenix permitem que *várias tabelas virtuais* partilhem uma tabela HBase física subjacente.

Criar uma vista Phoenix é semelhante à utilização da sintaxe de vista SQL padrão. Uma diferença é que pode definir colunas para a sua visão, além das colunas herdadas da sua tabela base. Também pode adicionar novas `KeyValue` colunas.

Por exemplo, aqui está uma tabela física com `product_metrics` o nome da seguinte definição:

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

Para adicionar mais colunas mais tarde, utilize a `ALTER VIEW` declaração.

### <a name="skip-scan"></a>Passar por digitalização

Skip scan usa uma ou mais colunas de um índice composto para encontrar valores distintos. Ao contrário de uma varredura de alcance, o skip scan implementa a digitalização intra-linha, produzindo [um melhor desempenho](https://phoenix.apache.org/performance.html#Skip-Scan). Durante a digitalização, o primeiro valor combinado é ignorado juntamente com o índice até que o valor seguinte seja encontrado.

Uma varredura de salto utiliza a `SEEK_NEXT_USING_HINT` enumeração do filtro HBase. Utilizando, `SEEK_NEXT_USING_HINT` a verificação de saltos mantém o registo de quais conjuntos de teclas, ou gamas de teclas, estão a ser procurados em cada coluna. A tomografia de salto leva então uma chave que lhe foi passada durante a avaliação do filtro, e determina se é uma das combinações. Caso contrário, o skip scan avalia a próxima chave mais alta para saltar.

### <a name="transactions"></a>Transações

Enquanto a HBase fornece transações de nível de linha, a Phoenix integra-se com a [Tephra](https://tephra.io/) para adicionar suporte de transação transversal e transversal com semântica [acid](https://en.wikipedia.org/wiki/ACID) completa.

Tal como acontece com as transações tradicionais de SQL, as transações fornecidas através do gestor de transações Phoenix permitem-lhe garantir que uma unidade atómica de dados é submetida a uma subida com sucesso, revirando a transação se a operação de upsert falhar em qualquer tabela ativada por transações.

Para permitir transações phoenix, consulte a documentação de [transação apache Phoenix](https://phoenix.apache.org/transactions.html).

Para criar uma nova tabela com transações habilitados, coloque o `TRANSACTIONAL` imóvel `true` em `CREATE` comunicado:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Para alterar uma tabela existente para ser transacional, utilize a mesma propriedade em `ALTER` comunicado:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Não é possível mudar uma tabela transacional para não transacional.

### <a name="salted-tables"></a>Mesas Salgadas

*O hotspotting do servidor da região* pode ocorrer ao escrever registos com chaves sequenciais para a Base H. Embora possa ter vários servidores de região no seu cluster, as suas escritas estão todas a ocorrer em apenas um. Esta concentração cria o problema de hotspotting onde, em vez de a sua carga de trabalho de escrita ser distribuída por todos os servidores da região disponível, apenas um está a lidar com a carga. Uma vez que cada região tem um tamanho máximo predefinido, quando uma região atinge esse limite de tamanho, é dividida em duas pequenas regiões. Quando isso acontece, uma destas novas regiões leva todos os novos recordes, tornando-se o novo hotspot.

Para mitigar este problema e obter um melhor desempenho, as tabelas pré-divididas para que todos os servidores da região sejam igualmente utilizados. Phoenix fornece *mesas salgadas,* adicionando transparentemente o byte de salga à chave da linha para uma mesa em particular. A tabela é pré-dividida sobre os limites do byte de sal para garantir uma distribuição de carga igual entre os servidores da região durante a fase inicial da tabela. Esta abordagem distribui a carga de trabalho de escrita em todos os servidores da região disponível, melhorando o desempenho da escrita e leitura. Para salgar uma mesa, especifique a `SALT_BUCKETS` propriedade da mesa quando a tabela é criada:

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

1. Para ativar ou desativar a Phoenix e controlar as definições de tempo limite de consulta da Phoenix, inicie sessão na UI web Ambari `https://YOUR_CLUSTER_NAME.azurehdinsight.net` () utilizando as credenciais de utilizador do Hadoop.

2. Selecione **HBase** da lista de serviços no menu à esquerda e, em seguida, selecione o **separador Configs.**

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png" alt-text="Configurações Apache Ambari HBase":::

3. Encontre a secção de configuração **Phoenix SQL** para ativar ou desativar phoenix e desative o tempo limite de consulta.

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png" alt-text="Secção de configuração Ambari Phoenix SQL":::

## <a name="see-also"></a>Ver também

* [Use Apache Phoenix com clusters HBase baseados em Linux em HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Use Apache Zeppelin para executar apaches Phoenix consultas sobre Apache HBase em Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
