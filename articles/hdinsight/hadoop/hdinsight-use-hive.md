---
title: O que é Apache Hive e HiveQL - Azure HDInsight
description: Apache Hive é um sistema de armazém de dados para Apache Hadoop. Pode consultar os dados armazenados na Hive usando o HiveQL, que se assemecia ao Transact-SQL. Neste documento, aprenda a usar Hive e HiveQL com Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 4e8c6b25055dfc38d56509e1744b8c7fcac40700
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944289"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>O que é Apache Hive e HiveQL em Azure HDInsight?

[Apache Hive](https://hive.apache.org/) é um sistema de armazém de dados para Apache Hadoop. A Colmeia permite a resumo, consulta e análise de dados. As consultas de colmeia são escritas em HiveQL, que é uma linguagem de consulta semelhante ao SQL.

A Colmeia permite-lhe projetar estrutura em dados em grande parte não estruturados. Depois de definir a estrutura, pode utilizar o HiveQL para consultar os dados sem conhecimento de Java ou MapReduce.

O HDInsight fornece vários tipos de cluster, que são sintonizados para cargas de trabalho específicas. Os seguintes tipos de agrupamento são mais utilizados para consultas de Colmeia:

|Tipo de cluster |Description|
|---|---|
|Interactive Query|Um cluster Hadoop que fornece uma funcionalidade [de processamento analítico de baixa latência (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) para melhorar os tempos de resposta para consultas interativas. Para obter mais informações, consulte o Início com Consulta Interativa no documento [HDInsight.](../interactive-query/apache-interactive-query-get-started.md)|
|Hadoop|Um cluster Hadoop que é sintonizado para cargas de trabalho de processamento de lotes. Para obter mais informações, consulte o [Início com Apache Hadoop no documento HDInsight.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)|
|Spark|A Apache Spark tem funcionalidade incorporada para trabalhar com a Hive. Para obter mais informações, consulte o Início com Apache Spark no documento [HDInsight.](../spark/apache-spark-jupyter-spark-sql.md)|
|HBase|O HiveQL pode ser usado para consultar dados armazenados em Apache HBase. Para obter mais informações, consulte o Início com Apache HBase no documento [HDInsight.](../hbase/apache-hbase-tutorial-get-started-linux.md)|

## <a name="how-to-use-hive"></a>Como usar a Colmeia

Use a seguinte tabela para descobrir as diferentes formas de usar a Colmeia com HDInsight:

| **Use este método** se quiser... | ... consultas **interativas** | ... processamento **de lotes** | ... deste **sistema operativo cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Ferramentas HDInsight para Código do Estúdio Visual](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X ou Windows |
| [Ferramentas HDInsight para Estúdio Visual](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Qualquer (baseado no navegador) |
| [Cliente beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X ou Windows |
| [API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Referência linguística HiveQL

A referência linguística HiveQL está disponível no manual de [idiomas.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

## <a name="hive-and-data-structure"></a>Colmeia e estrutura de dados

A Hive entende como trabalhar com dados estruturados e semi-estruturados. Por exemplo, ficheiros de texto em que os campos são delimitados por caracteres específicos. A seguinte declaração de HiveQL cria uma tabela sobre dados delimitados pelo espaço:

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

A Hive também suporta **serializer/desserializadores personalizados (SerDe) para dados complexos** ou estruturados irregularmente. Para obter mais informações, consulte o [Como utilizar um SerDe JSON personalizado com documento HDInsight.](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)

Para obter mais informações sobre formatos de ficheiros suportados pela Hive, consulte o [manual idioma https://cwiki.apache.org/confluence/display/Hive/LanguageManual) (](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Tabelas internas de colmeia vs tabelas externas

Existem dois tipos de tabelas que pode criar com a Colmeia:

* __Interno__: Os dados são armazenados no armazém de dados da Colmeia. O armazém de dados está localizado `/hive/warehouse/` no armazenamento padrão para o cluster.

    Utilize tabelas internas quando se aplicar uma das seguintes condições:

    * Os dados são temporários.
    * Quer que a Hive gere o ciclo de vida da tabela e dos dados.

* __Externo__: Os dados são armazenados fora do armazém de dados. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Utilize tabelas externas quando se aplicar uma das seguintes condições:

    * Os dados também são usados fora da Colmeia. Por exemplo, os ficheiros de dados são atualizados por outro processo (que não bloqueia os ficheiros.)
    * Os dados devem permanecer no local subjacente, mesmo depois de terem deixado cair a mesa.
    * Precisa de uma localização personalizada, como uma conta de armazenamento não padrão.
    * Um programa diferente da colmeia gere o formato de dados, localização, e assim por diante.

Para mais informações, consulte o post de blog de [Introdução de Mesas Internas e Externas da Hive.](/archive/blogs/cindygross/hdinsight-hive-internal-and-external-tables-intro)

## <a name="user-defined-functions-udf"></a>Funções definidas pelo utilizador (UDF)

A colmeia também pode ser estendida através **de funções definidas pelo utilizador (UDF)**. Um UDF permite-lhe implementar funcionalidade ou lógica que não é facilmente modelada no HiveQL. Para um exemplo de utilização de UDFs com Colmeia, consulte os seguintes documentos:

* [Utilize uma função definida pelo utilizador Java com a Colmeia Apache](../hadoop/apache-hadoop-hive-java-udf.md)

* [Utilize uma função definida pelo utilizador Python com a Colmeia Apache](../hadoop/python-udf-hdinsight.md)

* [Utilize uma função C# definida pelo utilizador com a Colmeia Apache](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma função personalizada definida pelo utilizador Apache Hive ao HDInsight](/archive/blogs/bigdatasupport/how-to-add-custom-hive-udfs-to-hdinsight)

* [Um exemplo da função definida pelo utilizador Apache Hive para converter formatos de data/hora para o relógio da Colmeia](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Dados de exemplo

A colmeia em HDInsight vem pré-carregada com uma tabela interna chamada `hivesampletable` . O HDInsight também fornece conjuntos de dados de exemplo que podem ser usados com a Hive. Estes conjuntos de dados são armazenados nos `/example/data` `/HdiSamples` diretórios e nos diretórios. Estes diretórios existem no armazenamento padrão para o seu cluster.

## <a name="example-hive-query"></a>Exemplo consulta de colmeia

As seguintes colunas do projeto de declarações da HiveQL no `/example/data/sample.log` ficheiro:

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

|Instrução |Descrição |
|---|---|
|DROP TABLE|Se a tabela já existir, elimine-a.|
|CRIAR TABELA EXTERNA|Cria uma nova tabela **externa** na Colmeia. As mesas externas armazenam apenas a definição de tabela na Colmeia. Os dados são deixados no local original e no formato original.|
|FORMATO DE LINHA|Diz à Hive como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.|
|ARMAZENADO COMO LOCALIZAÇÃO DE FICHEIRO DE TEXTO|Diz à Hive onde os dados são armazenados (o `example/data` diretório) e que é armazenado como texto. Os dados podem estar num ficheiro ou difundir-se em vários ficheiros dentro do diretório.|
|SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Esta afirmação devolve um valor de **3** porque existem três linhas que contêm este valor.|
|INPUT__FILE__NAME como '%.log'|A Colmeia tenta aplicar o esquema a todos os ficheiros do diretório. Neste caso, o diretório contém ficheiros que não correspondem ao esquema. Para prevenir dados de lixo nos resultados, esta declaração diz à Hive que devemos apenas devolver dados de ficheiros que terminam em .log.|

> [!NOTE]  
> As tabelas externas devem ser utilizadas quando se espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo automatizado de upload de dados ou operação MapReduce.
>
> Deixar cair uma tabela externa **não** apaga os dados, apenas elimina a definição de tabela.

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

Estas declarações executam as seguintes ações:

|Instrução |Descrição |
|---|---|
|CRIAR TABELA SE NÃO EXISTIR|Se a mesa não existe, crie-a. Como a palavra-chave **EXTERNA** não é utilizada, esta afirmação cria uma tabela interna. A tabela é armazenada no armazém de dados da Colmeia e é gerida completamente pela Hive.|
|ARMAZENADO COMO ORC|Armazena os dados no formato Colunar de Linha Otimizada (ORC). O ORC é um formato altamente otimizado e eficiente para armazenar dados da Hive.|
|INSIRA A SOBRESSAR ... SELECIONE|Seleciona linhas da tabela **log4jLogs** que contém **[ERROR]** e, em seguida, insere os dados na tabela **ErrorLogs.**|

> [!NOTE]  
> Ao contrário das tabelas externas, deixar cair uma tabela interna também elimina os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Melhorar o desempenho de consultas do Hive

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) é um quadro que permite que aplicações intensivas de dados, como a Hive, corram de forma muito mais eficiente em escala. O tez é ativado por defeito.  A [Colmeia Apache nos documentos de design do Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contém detalhes sobre as opções de implementação e configurações de afinação.

### <a name="low-latency-analytical-processing-llap"></a>Baixo processamento analítico de latência (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (por vezes conhecido como Live Long and Process) é uma nova funcionalidade na Hive 2.0 que permite o caching na memória de consultas. LLAP torna as consultas de Hive muito mais rápidas, até [26x mais rápido que a Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

O HDInsight fornece LLAP no tipo de cluster de consulta interativa. Para mais informações, consulte o documento [Start with Interactive Query.](../interactive-query/apache-interactive-query-get-started.md)

## <a name="scheduling-hive-queries"></a>Agendar consultas do Hive

Existem vários serviços que podem ser usados para executar consultas de Colmeia como parte de um fluxo de trabalho programado ou a pedido.

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory permite-lhe utilizar o HDInsight como parte de um oleoduto da Data Factory. Para obter mais informações sobre a utilização da Colmeia a partir de um oleoduto, consulte os dados da Transform utilizando a atividade da Hive no documento [Azure Data Factory.](../../data-factory/transform-data-using-hadoop-hive.md)

### <a name="hive-jobs-and-sql-server-integration-services"></a>Trabalhos de colmeia e serviços de integração de servidores SQL

Pode utilizar os Serviços de Integração de Servidores SQL (SSIS) para executar um trabalho de Colmeia. O Azure Feature Pack for SSIS fornece os seguintes componentes que trabalham com trabalhos de Colmeia em HDInsight.

* [Tarefa de Colmeia Azure HDInsight](/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Gestor de conexão de assinatura Azure](/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Para mais informações, consulte a documentação do [Azure Feature Pack.](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de trabalho e coordenação que gere os empregos de Hadoop. Para obter mais informações sobre a utilização de Oozie com Hive, consulte o [Use Apache Oozie para definir e executar um](../hdinsight-use-oozie-linux-mac.md) documento de fluxo de trabalho.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o que é a Hive e como usá-la com Hadoop em HDInsight, use os seguintes links para explorar outras formas de trabalhar com a Azure HDInsight.

* [Upload data to HDInsight (Carregar dados para o HDInsight)](../hdinsight-upload-data.md)
* [Use funções definidas do utilizador Python (UDF) com a Colmeia Apache e o Porco Apache em HDInsight](./python-udf-hdinsight.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)