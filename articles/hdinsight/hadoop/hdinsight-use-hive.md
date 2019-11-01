---
title: O que é Apache Hive e HiveQL-Azure HDInsight
description: Apache Hive é um sistema de data warehouse para Apache Hadoop. Você pode consultar os dados armazenados no hive usando HiveQL, que é semelhante ao Transact-SQL. Neste documento, saiba como usar o hive e o HiveQL com o Azure HDInsight.
keywords: hiveql, o que é Hive, Hadoop hiveql, como usar o Hive, Learn Hive, o que é Hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: aa3e3b63bdfda7aa6d875055dee4c69b9840db25
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "72167359"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>O que é Apache Hive e HiveQL no Azure HDInsight?

[Apache Hive](https://hive.apache.org/) é um sistema de data warehouse para Apache Hadoop. O hive permite o resumo de dados, a consulta e a análise de dados. As consultas do hive são escritas em HiveQL, que é uma linguagem de consulta semelhante ao SQL.

O hive permite que você projeto a estrutura em dados grandes não estruturados. Depois de definir a estrutura, você pode usar HiveQL para consultar os dados sem conhecimento de Java ou MapReduce.

O HDInsight fornece vários tipos de cluster, que são ajustados para cargas de trabalho específicas. Os seguintes tipos de cluster são usados com mais frequência em consultas de Hive:

* __Consulta interativa__: um cluster Hadoop que fornece funcionalidade de [LLAP (processamento analítico de baixa latência)](https://cwiki.apache.org/confluence/display/Hive/LLAP) para melhorar os tempos de resposta para consultas interativas. Para obter mais informações, consulte o documento [Iniciar com a consulta interativa no HDInsight](../interactive-query/apache-interactive-query-get-started.md) .

* __Hadoop__: um cluster Hadoop que é ajustado para cargas de trabalho de processamento em lote. Para obter mais informações, consulte o documento [Iniciar com o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) .

* __Spark__: o Apache Spark tem funcionalidade interna para trabalhar com o hive. Para obter mais informações, consulte o documento [Iniciar com Apache Spark no HDInsight](../spark/apache-spark-jupyter-spark-sql.md) .

* __HBase__: HiveQL pode ser usado para consultar dados armazenados no Apache HBase. Para obter mais informações, consulte o documento [Iniciar com o Apache HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) .

## <a name="how-to-use-hive"></a>Como usar o hive

Use a tabela a seguir para descobrir as diferentes maneiras de usar o hive com o HDInsight:

| **Use esse método** se desejar... | ... consultas **interativas** | ... processamento **em lotes** | ... deste **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Ferramentas do HDInsight para Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, UNIX, Mac OS X ou Windows |
| [Ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Qualquer (baseado em navegador) |
| [Cliente beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, UNIX, Mac OS X ou Windows |
| [API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, UNIX, Mac OS X ou Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Referência de linguagem HiveQL

A referência de linguagem HiveQL está disponível no [manual de idioma](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive e estrutura de dados

O hive compreende como trabalhar com dados estruturados e semiestruturados. Por exemplo, arquivos de texto em que os campos são delimitados por caracteres específicos. A seguinte instrução HiveQL cria uma tabela com dados delimitados por espaço:

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

O hive também dá suporte a **SerDe (serializador/desserializadores personalizados)** para dados complexos ou estruturados de irregularidades. Para obter mais informações, consulte o documento [como usar um SERDE JSON personalizado com o HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) .

Para obter mais informações sobre formatos de arquivo com suporte no hive, consulte o [manual de idioma (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Tabelas internas do hive vs. tabelas externas

Há dois tipos de tabelas que você pode criar com o hive:

* __Interno__: os dados são armazenados no data warehouse do hive. O data warehouse está localizado em `/hive/warehouse/` no armazenamento padrão do cluster.

    Use tabelas internas quando uma das seguintes condições se aplicar:

    * Os dados são temporários.
    * Você deseja que o hive gerencie o ciclo de vida da tabela e dos dados.

* __Externo__: os dados são armazenados fora do data warehouse. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Use tabelas externas quando uma das seguintes condições se aplicar:

    * Os dados também são usados fora do hive. Por exemplo, os arquivos de dados são atualizados por outro processo (que não bloqueia os arquivos).
    * Os dados precisam permanecer no local subjacente, mesmo depois de descartar a tabela.
    * Você precisa de um local personalizado, como uma conta de armazenamento não padrão.
    * Um programa que não seja o hive gerencia o formato de dados, o local, etc.

Para obter mais informações, consulte a postagem do blog [introdução às tabelas internas e externas do hive](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/) .

## <a name="user-defined-functions-udf"></a>UDF (funções definidas pelo usuário)

O hive também pode ser estendido por meio **de UDF (funções definidas pelo usuário)** . Um UDF permite que você implemente a funcionalidade ou a lógica que não é facilmente modelada no HiveQL. Para obter um exemplo de como usar UDFs com o Hive, consulte os seguintes documentos:

* [Usar uma função definida pelo usuário do Java com Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Usar uma função definida pelo usuário do Python com Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Usar uma C# função definida pelo usuário com Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma função personalizada Apache Hive definida pelo usuário ao HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Um exemplo Apache Hive função definida pelo usuário para converter formatos de data/hora em carimbo de hora do hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Dados de exemplo

O hive no HDInsight vem pré-carregado com uma tabela interna denominada `hivesampletable`. O HDInsight também fornece conjuntos de dados de exemplo que podem ser usados com o hive. Esses conjuntos de dados são armazenados nos diretórios `/example/data` e `/HdiSamples`. Esses diretórios existem no armazenamento padrão para seu cluster.

## <a id="job"></a>Exemplo de consulta de Hive

As instruções HiveQL a seguir projetam colunas no arquivo `/example/data/sample.log`:

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

No exemplo anterior, as instruções HiveQL executam as seguintes ações:

* `DROP TABLE`: se a tabela já existir, exclua-a.

* `CREATE EXTERNAL TABLE`: cria uma nova tabela **externa** no hive. As tabelas externas armazenam apenas a definição de tabela no hive. Os dados são deixados no local original e no formato original.

* `ROW FORMAT`: informa ao hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

* `STORED AS TEXTFILE LOCATION`: informa ao hive onde os dados são armazenados (o diretório `example/data`) e que são armazenados como texto. Os dados podem estar em um arquivo ou distribuídos em vários arquivos dentro do diretório.

* `SELECT`: seleciona uma contagem de todas as linhas em que a coluna **T4** contém o valor **[ERROR]** . Essa instrução retorna um valor de **3** porque há três linhas que contêm esse valor.

* `INPUT__FILE__NAME LIKE '%.log'`-Hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados de lixo nos resultados, essa instrução informa ao hive que só devemos retornar dados de arquivos que terminam em. log.

> [!NOTE]  
> As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
>
> Descartar uma tabela externa **não** exclui os dados, apenas exclui a definição da tabela.

Para criar uma tabela **interna** em vez de external, use o seguinte HiveQL:

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

Essas instruções executam as seguintes ações:

* `CREATE TABLE IF NOT EXISTS`: se a tabela não existir, crie-a. Como a palavra-chave **external** não é usada, essa instrução cria uma tabela interna. A tabela é armazenada no data warehouse do hive e é gerenciada completamente pelo Hive.

* `STORED AS ORC`: armazena os dados no formato colunar de linha otimizado (ORC). O ORC é um formato altamente otimizado e eficiente para armazenar dados do hive.

* `INSERT OVERWRITE ... SELECT`: seleciona linhas da tabela **log4jLogs** que contém **[ERROR]** e insere os dados na tabela de logs de **erros** .

> [!NOTE]  
> Ao contrário das tabelas externas, a remoção de uma tabela interna também exclui os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Melhorar o desempenho da consulta do hive

### <a id="usetez"></a>Apache Tez

[Apache tez](https://tez.apache.org) é uma estrutura que permite que aplicativos com uso intensivo de dados, como o Hive, sejam executados de forma muito mais eficiente em escala. O tez é habilitado por padrão.  O [Apache Hive em documentos de design tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contém detalhes sobre as opções de implementação e configurações de ajuste.

### <a name="low-latency-analytical-processing-llap"></a>LLAP (processamento analítico de baixa latência)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (às vezes conhecido como Live Long e Process) é um novo recurso no hive 2,0 que permite o cache em memória de consultas. O LLAP torna as consultas de Hive muito mais rápidas, até [26x mais rápidas do que o hive 1. x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

O HDInsight fornece LLAP no tipo de cluster de consulta interativa. Para obter mais informações, consulte o documento [Iniciar com consulta interativa](../interactive-query/apache-interactive-query-get-started.md) .

## <a name="scheduling-hive-queries"></a>Agendando consultas do hive

Há vários serviços que podem ser usados para executar consultas do hive como parte de um fluxo de trabalho agendado ou sob demanda.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory permite que você use o HDInsight como parte de um pipeline de Data Factory. Para obter mais informações sobre como usar o hive de um pipeline, consulte a [atividade transformar dados usando o hive no Azure data Factory](../../data-factory/transform-data-using-hadoop-hive.md) documento.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Trabalhos do hive e SQL Server Integration Services

Você pode usar SQL Server Integration Services (SSIS) para executar um trabalho do hive. O Azure Feature Pack para SSIS fornece os seguintes componentes que funcionam com trabalhos do hive no HDInsight.

* [Tarefa do hive do Azure HDInsight](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Gerenciador de conexões de assinatura do Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Para obter mais informações, consulte a documentação do [Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis) .

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie é um sistema de fluxo de trabalho e coordenação que gerencia trabalhos do Hadoop. Para obter mais informações sobre como usar o Oozie com o Hive, consulte [usar o Apache Oozie para definir e executar um documento de fluxo de trabalho](../hdinsight-use-oozie-linux-mac.md) .

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu o que é o hive e como usá-lo com o Hadoop no HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight](../hdinsight-upload-data.md)
* [Usar funções definidas pelo usuário do Python (UDF) com Apache Hive e Apache Pig no HDInsight](./python-udf-hdinsight.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
