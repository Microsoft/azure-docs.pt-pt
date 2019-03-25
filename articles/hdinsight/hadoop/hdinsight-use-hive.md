---
title: O que é o Apache Hive e o HiveQL - Azure HDInsight
description: Apache Hive é um sistema de armazém de dados para o Apache Hadoop. Pode consultar os dados armazenados no Hive com o HiveQL, que semelhante para o Transact-SQL. Neste documento, saiba como utilizar o Hive e o HiveQL com o Azure HDInsight.
keywords: hiveql, o que é o hive, hiveql do hadoop, como utilizar o hive, saiba o hive, o que é o hive
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 5b5f323e20bbaf91c151641f8dc78fbc8cde8c24
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351648"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>O que é o Apache Hive e o HiveQL no HDInsight do Azure?

[Apache Hive](https://hive.apache.org/) é um sistema de armazém de dados para o Apache Hadoop. Hive permite que o resumo de dados, consulta e análise de dados. Consultas do Hive são escritas em HiveQL, que é uma linguagem de consulta semelhante ao SQL.

Hive permite-lhe a estrutura do projeto nos dados em grande parte não estruturados. Depois de definir a estrutura, pode utilizar o HiveQL para consultar os dados sem o conhecimento de Java ou do MapReduce.

HDInsight fornece vários tipos de cluster, que são otimizados para cargas de trabalho específicas. Os seguintes tipos de cluster são geralmente utilizados para consultas do Hive:

* __Consulta interativa__: Um cluster do Hadoop que fornece [baixa latência analítico de processamento (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funcionalidade para melhorar os tempos de resposta para consultas interativas. Para obter mais informações, consulte a [introdução ao Interactive Query no HDInsight](../interactive-query/apache-interactive-query-get-started.md) documento.

* __Hadoop__: Um cluster do Hadoop concebido para cargas de trabalho de processamento em lotes. Para obter mais informações, consulte a [começar com o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) documento.

* __Spark__: Apache Spark tem uma funcionalidade incorporada para trabalhar com o Hive. Para obter mais informações, consulte a [começar com o Apache Spark no HDInsight](../spark/apache-spark-jupyter-spark-sql.md) documento.

* __HBase__: Pode ser utilizado o HiveQL para consultar dados armazenados no Apache HBase. Para obter mais informações, consulte a [começar com o Apache HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) documento.

## <a name="how-to-use-hive"></a>Como utilizar o Hive

Utilize a tabela seguinte para detetar as diferentes formas de utilizar o Hive com HDInsight:

| **Utilize este método** se pretender que... | ... **interativo** consultas | ... **batch** processamento | ... .with isso **sistema operativo do cluster** | ... .from isso **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Ferramentas do HDInsight para Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X ou Windows |
| [Ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Linux ou Windows * |Windows |
| [Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualquer (baseadas no browser) |
| [Cliente de beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows * |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows * |Windows |

> [!IMPORTANT]
> \* Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou superior. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="hiveql-language-reference"></a>Referência de linguagem de HiveQL

Referência de linguagem de HiveQL está disponível na [manual de idioma (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Estrutura de dados e do Hive

Hive compreende como trabalhar com dados estruturados e semiestruturados. Por exemplo, ficheiros de texto em que os campos são delimitados por carateres específicos. A seguinte instrução de HiveQL cria uma tabela através de dados delimitada por espaços:

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

Hive também oferece suporte a custom **serializador/desserializadores (SerDe)** para dados complexos ou forma irregular. Para obter mais informações, consulte a [como utilizar um SerDe de JSON personalizado com o HDInsight](https://web.archive.org/web/20190217104719/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) documento.

Para obter mais informações sobre formatos de ficheiro suportados do Hive, consulte o [(idioma de manuais https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Tabelas externas do vs tabelas internas do Hive

Existem dois tipos de tabelas que pode criar com o Hive:

* __Interno__: Dados são armazenados no armazém de dados do Hive. O armazém de dados está localizado em `/hive/warehouse/` no armazenamento padrão para o cluster.

    Utilize tabelas internas, quando uma das seguintes condições aplicam-se:

    * Os dados são temporários.
    * Pretende que o Hive para gerir o ciclo de vida da tabela e os dados.

* __Externo__: Dados são armazenados fora do armazém de dados. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Utilize tabelas externas, quando uma das seguintes condições aplicam-se:

    * Os dados também são utilizados fora do Hive. Por exemplo, os ficheiros de dados são atualizados por outro processo (que não bloquear os arquivos.)
    * Dados têm de permanecer na localização subjacente, mesmo depois de remover a tabela.
    * Precisa de um local personalizado, como uma conta de armazenamento não predefinido.
    * Um programa que não seja o ramo de registo gere o formato de dados, localização, etc.

Para obter mais informações, consulte a [Hive interno e externo de tabelas de introdução] [ cindygross-hive-tables] postagem de blog.

## <a name="user-defined-functions-udf"></a>Funções definidas pelo utilizador (UDF)

Ramo de registo também pode ser estendido por meio **as funções definidas pelo utilizador (UDF)**. Uma UDF permite que implemente a funcionalidade ou a lógica que não é facilmente modelada no HiveQL. Para obter um exemplo do uso UDFs com o Hive, veja os documentos seguintes:

* [Utilizar uma função definida pelo utilizador do Java com o Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Utilizar uma função definida pelo utilizador do Python com o Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Utilize um C# função definida pelo utilizador com o Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma Apache Hive definidas pelo utilizador função personalizada ao HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Um exemplo do Apache Hive função definida pelo utilizador para converter formatos de data/hora para Hive timestamp](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Dados de exemplo

Hive no HDInsight estão pré-carregados com uma tabela interna com o nome `hivesampletable`. HDInsight também fornece conjuntos de dados de exemplo que pode ser utilizados com o Hive. Estes conjuntos de dados são armazenados no `/example/data` e `/HdiSamples` diretórios. Esses diretórios existem no armazenamento padrão para o seu cluster.

## <a id="job"></a>Consulta do Hive de exemplo

As seguintes declarações HiveQL projeto colunas para o `/example/data/sample.log` ficheiro:

```hiveql
set hive.execution.engine=tez;
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

No exemplo anterior, as declarações HiveQL efetuar as seguintes ações:

* `set hive.execution.engine=tez;`: Define o motor de execução para utilizar o Apache Tez. Usando o Tez, pode fornecer um aumento no desempenho da consulta. Para obter mais informações sobre o Tez, consulte a [utilizar o Apache Tez para um melhor desempenho](#usetez) secção.

    > [!NOTE]  
    > Esta instrução só é necessário quando utilizar um cluster do HDInsight baseado em Windows. Tez é o motor de execução padrão para o HDInsight baseado em Linux.

* `DROP TABLE`: Se a tabela já existir, elimine-o.

* `CREATE EXTERNAL TABLE`: Cria uma nova **externo** tabela do Hive. Tabelas externas apenas armazenam a definição da tabela no Hive. Os dados são deixados na localização original e no formato original.

* `ROW FORMAT`: Informa ao Hive como os dados estiverem formatados. Neste caso, os campos em cada registo são separados por um espaço.

* `STORED AS TEXTFILE LOCATION`: Indica onde estão armazenados os dados de Hive (o `example/data` diretório) e que são armazenados como texto. Os dados podem ser num arquivo ou distribuídas entre vários arquivos dentro do diretório.

* `SELECT`: Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[erro]**. Esta declaração devolve um valor de **3** porque existem três linhas que contêm este valor.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive tenta aplicar o esquema a todos os ficheiros no diretório. Neste caso, o diretório contém ficheiros que não corresponde ao esquema. Para impedir que os dados de lixo nos resultados, essa instrução diz ao Hive que podemos deverá devolver apenas dados de ficheiros terminados em. log.

> [!NOTE]  
> Tabelas externas devem ser usadas quando espera que os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automatizada, ou operação de MapReduce.
>
> Remover uma tabela externa faz **não** eliminar os dados, apenas elimina a definição da tabela.

Para criar uma **interno** de tabela em vez de externo, utilize o HiveQL seguinte:

```hiveql
set hive.execution.engine=tez;
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

* `CREATE TABLE IF NOT EXISTS`: Se a tabela não existir, crie-a. Uma vez que o **externo** palavra-chave não é utilizado, esta instrução cria uma tabela interna. A tabela é armazenada no armazém de dados de Hive e é gerenciada completamente o Hive.

* `STORED AS ORC`: Armazena os dados no formato otimizado linhas em colunas (ORC). ORC é um formato altamente otimizado e eficiente para armazenar os dados de Hive.

* `INSERT OVERWRITE ... SELECT`: Seleciona as linhas do **log4jLogs** tabela que contém **[erro]** e, em seguida, insere os dados para o **registos de erros** tabela.

> [!NOTE]  
> Ao contrário das tabelas externas, remover uma tabela interna também elimina os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Melhorar o desempenho de consulta do Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) é uma estrutura que permite que aplicações intensivas em termos de dados, como o Hive, sejam executados de maneira muito mais eficiente em escala. Tez está ativado por predefinição para os clusters do HDInsight baseado em Linux.

> [!NOTE]  
> Tez está atualmente desativada por predefinição para os clusters do HDInsight baseado em Windows e tem de estar ativada. Para tirar partido do Tez, o seguinte valor tem de ser definido para uma consulta do Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez é o mecanismo de padrão para os clusters do HDInsight baseado em Linux.

O [Apache Hive no Tez criar documentos](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contém detalhes sobre as opções de implementação e configurações de otimização.

Para auxiliar na depuração de tarefas foi executada com Tez, o HDInsight fornece a interface do Usuário que permite-lhe ver os detalhes das tarefas do Tez da web seguinte: [Utilize a vista do Apache Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md).

### <a name="low-latency-analytical-processing-llap"></a>Processamento analítico de baixa latência (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (por vezes conhecida como Live Long and Process) é uma funcionalidade nova no 2.0 Hive, que permite a colocação em cache de memória de consultas. LLAP torna muito mais rápidas, até de consultas do Hive [26 x mais rapidamente do que o Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight fornece LLAP, o tipo de cluster do Interactive Query. Para obter mais informações, consulte a [introdução ao Interactive Query](../interactive-query/apache-interactive-query-get-started.md) documento.

## <a name="scheduling-hive-queries"></a>Agendamento de consultas do Hive

Existem vários serviços que podem ser utilizados para executar consultas do Hive como parte de um fluxo de trabalho agendado ou a pedido.

### <a name="azure-data-factory"></a>Azure Data Factory

O Azure Data Factory permite-lhe utilizar o HDInsight como parte de um pipeline do Data Factory. Para obter mais informações sobre a utilização do Hive a partir de um pipeline, consulte a [transformar dados com a atividade do Hive no Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) documento.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Tarefas do Hive e o SQL Server Integration Services

Pode usar o SQL Server Integration Services (SSIS) para executar uma tarefa do Hive. O pacote de funcionalidades do Azure para SSIS fornece os seguintes componentes que funcionam com tarefas do Hive no HDInsight.

* [Tarefas do Hive do HDInsight do Azure][hivetask]

* [Gestor de ligação de subscrição do Azure][connectionmanager]

Para obter mais informações, consulte a [pacote de funcionalidades do Azure] [ ssispack] documentação.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de fluxo de trabalho e a coordenação que gere as tarefas do Hadoop. Para obter mais informações sobre como utilizar o Oozie com o Hive, consulte a [utilização Apache Oozie para definir e executar um fluxo de trabalho](../hdinsight-use-oozie-linux-mac.md) documento.

## <a id="nextsteps"></a>Passos seguintes

Agora que aprendeu o que é o Hive e como usá-lo com o Hadoop no HDInsight, utilize as seguintes ligações para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Utilizar o Apache Pig com o HDInsight][hdinsight-use-pig]
* [Utilizar tarefas de MapReduce com o HDInsight][hdinsight-use-mapreduce]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: https://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
