---
title: O que é Apache Hive e HiveQL - Azure HDInsight
description: Apache Hive é um sistema de armazém de dados para Apache Hadoop. Pode consultar dados armazenados na Hive utilizando a HiveQL, que se similar à Transact-SQL. Neste documento, aprenda a usar a Hive e a HiveQL com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: f7dc7b520cba2bbf2351d93795a1a26b3b5124be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471358"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>O que é Apache Hive e HiveQL no Azure HDInsight?

[Apache Hive](https://hive.apache.org/) é um sistema de armazém de dados para Apache Hadoop. A Hive permite a resumição de dados, consulta e análise de dados. As consultas de colmeia são escritas em HiveQL, que é uma linguagem de consulta semelhante à SQL.

A Hive permite-lhe projetar a estrutura em dados largamente desestruturados. Depois de definir a estrutura, pode usar o HiveQL para consultar os dados sem conhecimento de Java ou MapReduce.

O HDInsight fornece vários tipos de cluster, que são sintonizados para cargas de trabalho específicas. Os seguintes tipos de cluster são mais frequentemente utilizados para consultas da Colmeia:

|Tipo de cluster |Descrição|
|---|---|
|Interactive Query|Um cluster Hadoop que fornece a funcionalidade [de processamento analítico de baixa latência (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) para melhorar os tempos de resposta para consultas interativas. Para mais informações, consulte o Início com Consulta Interativa no documento [HDInsight.](../interactive-query/apache-interactive-query-get-started.md)|
|Hadoop|Um aglomerado hadoop que está sintonizado para o processamento de cargas de trabalho de lote. Para mais informações, consulte o [Início com Apache Hadoop no documento HDInsight.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)|
|Spark|A Apache Spark tem uma funcionalidade incorporada para trabalhar com a Hive. Para mais informações, consulte o Início com A Faísca Apache no documento [HDInsight.](../spark/apache-spark-jupyter-spark-sql.md)|
|HBase|O HiveQL pode ser usado para consultar dados armazenados em Apache HBase. Para mais informações, consulte o Início com Apache HBase no documento [HDInsight.](../hbase/apache-hbase-tutorial-get-started-linux.md)|

## <a name="how-to-use-hive"></a>Como usar a Colmeia

Utilize a tabela seguinte para descobrir as diferentes formas de utilizar a Hive com o HDInsight:

| **Use este método** se quiser... | ... consultas **interativas** | ... processamento de **lotes** | ... deste **sistema operativo cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Ferramentas HDInsight para Código de Estúdio Visual](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X ou Windows |
| [Ferramentas HDInsight para Estúdio Visual](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Qualquer (baseado no navegador) |
| [Cliente beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X ou Windows |
| [API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Referência linguística hiveQL

A referência linguística hiveQL está disponível no manual de [idiomas.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

## <a name="hive-and-data-structure"></a>Colmeia e estrutura de dados

A Hive entende como trabalhar com dados estruturados e semi-estruturados. Por exemplo, ficheiros de texto onde os campos são delimitados por caracteres específicos. A seguinte declaração da HiveQL cria uma tabela sobre dados delimitados pelo espaço:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

A Hive também suporta **serializers/desserializers personalizados (SerDe)** para dados complexos ou estruturados irregularmente. Para mais informações, consulte o [Como utilizar um JSON SerDe personalizado com documento HDInsight.](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)

Para obter mais informações sobre formatos de ficheirosuportados pela Hive, consulte o [manual de Idiomas (https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Mesas internas da colmeia vs mesas externas

Existem dois tipos de tabelas que pode criar com a Colmeia:

* __Interna__: Os dados são armazenados no armazém de dados da Hive. O armazém de `/hive/warehouse/` dados está localizado no armazenamento padrão do cluster.

    Utilize tabelas internas quando uma das seguintes condições se aplicar:

    * Os dados são temporários.
    * Quer que a Hive gere o ciclo de vida da tabela e dos dados.

* __Externo__: Os dados são armazenados fora do armazém de dados. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Utilize tabelas externas quando uma das seguintes condições se aplicar:

    * Os dados também são usados fora da Colmeia. Por exemplo, os ficheiros de dados são atualizados por outro processo (que não bloqueia os ficheiros.)
    * Os dados têm de permanecer no local subjacente, mesmo depois de terem deixado cair a mesa.
    * Precisa de uma localização personalizada, como uma conta de armazenamento não predefinida.
    * Um programa que não seja a colmeia gere o formato de dados, localização, e assim por diante.

Para mais informações, consulte a publicação de blog intro da [Hive Internal and External Tables.](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/)

## <a name="user-defined-functions-udf"></a>Funções definidas pelo utilizador (UDF)

A colmeia também pode ser estendida através de **funções definidas pelo utilizador (UDF)**. Uma UDF permite implementar funcionalidade ou lógica que não é facilmente modelada no HiveQL. Para um exemplo de utilização de UDFs com Colmeia, consulte os seguintes documentos:

* [Utilize uma função java definida pelo utilizador com a Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Utilize uma função definida pelo utilizador Python com a Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Utilize uma função c# definida pelo utilizador com a Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma função personalizada apache Hive definida pelo utilizador ao HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Um exemplo Apache Hive função definida pelo utilizador para converter formatos de data/hora para carimbo de tempo da Colmeia](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Dados de exemplo

A colmeia no HDInsight vem pré-carregada `hivesampletable`com uma tabela interna chamada . O HDInsight também fornece conjuntos de dados de exemplo que podem ser usados com a Hive. Estes conjuntos de dados `/example/data` são `/HdiSamples` armazenados nos diretórios e diretórios. Estes diretórios existem no armazenamento padrão para o seu cluster.

## <a name="example-hive-query"></a>Consulta de colmeia de exemplo

As seguintes colunas de projeto de `/example/data/sample.log` declarações da HiveQL no ficheiro:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

No exemplo anterior, as declarações da HiveQL realizam as seguintes ações:

|Declaração |Descrição |
|---|---|
|MESA DE LANÇAMENTO|Se a mesa já existir, apague-a.|
|CRIAR TABELA EXTERNA|Cria uma nova mesa **externa** na Colmeia. As tabelas externas apenas armazenam a definição de mesa na Colmeia. Os dados são deixados na localização original e no formato original.|
|FORMATO DE LINHA|Diz à Hive como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.|
|LOCALIZAÇÃO ARMAZENADA COMO FICHEIRO DE TEXTO|Diz à Hive onde os `example/data` dados são armazenados (o diretório) e que são armazenados como texto. Os dados podem estar num ficheiro ou espalhados por vários ficheiros dentro do diretório.|
|SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Esta declaração devolve um valor de **3** porque existem três linhas que contêm este valor.|
|INPUT__FILE__NAME LIKE '%.log'|A Hive tenta aplicar o esquema a todos os ficheiros do diretório. Neste caso, o diretório contém ficheiros que não correspondem ao esquema. Para evitar dados de lixo nos resultados, esta declaração diz à Hive que só devemos devolver dados de ficheiros que terminam em .log.|

> [!NOTE]  
> As tabelas externas devem ser utilizadas quando se espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo automatizado de upload de dados, ou operação MapReduce.
>
> Deixar cair uma tabela externa **não** elimina os dados, apenas elimina a definição da tabela.

Para criar uma tabela **interna** em vez de externa, utilize o seguinte HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Estas declarações realizam as seguintes ações:

|Declaração |Descrição |
|---|---|
|CRIAR TABELA SE NÃO EXISTIR|Se a mesa não existe, crie-a. Como a palavra-chave **externa** não é usada, esta declaração cria uma tabela interna. A mesa é armazenada no armazém de dados da Hive e é gerida completamente pela Hive.|
|ARMAZENADO COMO ORC|Armazene os dados em formato Colunar de Linhas Otimizada (ORC). OrC é um formato altamente otimizado e eficiente para armazenar dados da Hive.|
|INSERIR SOBREESCRITA ... SELECIONE|Seleciona linhas da tabela **log4jLogs** que contém **[ERROR],** e depois insere os dados na tabela **errorLogs.**|

> [!NOTE]  
> Ao contrário das tabelas externas, a queda de uma tabela interna também elimina os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Melhorar o desempenho de consultas do Hive

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) é um quadro que permite que aplicações intensivas de dados, como a Hive, sejam executadas de forma muito mais eficiente em escala. Tez é ativado por padrão.  Os documentos de [design Apache Hive em Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contêm detalhes sobre as escolhas de implementação e configurações de afinação.

### <a name="low-latency-analytical-processing-llap"></a>Processamento analítico de baixa latência (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (por vezes conhecido como Live Long and Process) é uma nova funcionalidade na Colmeia 2.0 que permite o cache na memória de consultas. Llap faz as consultas da Hive muito mais rápidas, até [26x mais rápido que a Hive 1.x em alguns casos.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)

O HDInsight fornece LLAP no tipo de cluster de consulta interativa. Para mais informações, consulte o [documento Iniciar com Consulta Interativa.](../interactive-query/apache-interactive-query-get-started.md)

## <a name="scheduling-hive-queries"></a>Agendar consultas do Hive

Existem vários serviços que podem ser usados para executar consultas da Hive como parte de um fluxo de trabalho programado ou a pedido.

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory permite-lhe utilizar o HDInsight como parte de um oleoduto data factory. Para obter mais informações sobre a utilização da Hive a partir de um oleoduto, consulte os dados transformos utilizando a atividade da Hive no documento da Fábrica de [Dados Azure.](../../data-factory/transform-data-using-hadoop-hive.md)

### <a name="hive-jobs-and-sql-server-integration-services"></a>Empregos de colmeia e serviços de integração de servidores SQL

Pode utilizar os Serviços de Integração do Servidor SQL (SSIS) para executar um trabalho de Colmeia. O Pack de Recursos Azure para SSIS fornece os seguintes componentes que trabalham com trabalhos da Hive no HDInsight.

* [Tarefa de colmeia Azure HDInsight](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Gestor de conexão de assinatura Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Para mais informações, consulte a documentação do [Pack de Recursos Azure.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de fluxo de trabalho e coordenação que gere os empregos de Hadoop. Para obter mais informações sobre a utilização de Oozie com Hive, consulte o [Use Apache Oozie para definir e executar um](../hdinsight-use-oozie-linux-mac.md) documento de fluxo de trabalho.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o que é a Hive e como usá-la com Hadoop no HDInsight, use os seguintes links para explorar outras formas de trabalhar com o Azure HDInsight.

* [Upload data to HDInsight (Carregar dados para o HDInsight)](../hdinsight-upload-data.md)
* [Utilize funções definidas pelo utilizador Python (UDF) com hiv apache e porco Apache em HDInsight](./python-udf-hdinsight.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
