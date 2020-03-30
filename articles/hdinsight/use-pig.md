---
title: Utilizar Apache Pig
titleSuffix: Azure HDInsight
description: Aprenda a usar o Porco com Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672128"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Use o Porco Apache com Hadoop Apache no HDInsight

Aprenda a usar [o Porco Apache](https://pig.apache.org/) com HDInsight.

Apache Pig é uma plataforma para criar programas para Apache Hadoop usando uma linguagem processual conhecida como *Pig Latin*. O porco é uma alternativa a Java para criar soluções *MapReduce,* e está incluído com Azure HDInsight. Utilize a tabela seguinte para descobrir as várias formas de o Porco poder ser utilizado com o HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Por que usar o Porco Apache

Um dos desafios do processamento de dados utilizando mapReduce em Hadoop é implementar a sua lógica de processamento usando apenas um mapa e uma função de redução. Para um processamento complexo, muitas vezes tem de quebrar o processamento em múltiplas operações mapReduce que estão acorrentadas para alcançar o resultado desejado.

O porco permite-lhe definir o processamento como uma série de transformações que os dados fluem para produzir a saída desejada.

A língua latina do porco permite-lhe descrever o fluxo de dados da entrada crua, através de uma ou mais transformações, para produzir a saída desejada. Os programas pig latinos seguem este padrão geral:

* **Carga**: Leia os dados a manipular a partir do sistema de ficheiros.

* **Transformar:** Manipular os dados.

* **Despejo ou armazene:** Dados de saída para o ecrã ou armazená-lo para processamento.

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

O Pig Latin também suporta funções definidas pelo utilizador (UDF), que lhe permitem invocar componentes externos que implementam lógicas difíceis de modelar em Pig Latin.

Para mais informações sobre o Pig Latin, consulte o Manual de [Referência Latina do Porco 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) e o Manual de Referência Latina do Porco [2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados `/example/data` `/HdiSamples` de exemplo, que são armazenados nos diretórios e diretórios. Estes diretórios estão no armazenamento padrão para o seu cluster. O exemplo pig neste documento utiliza o `/example/data/sample.log`ficheiro *log4j* a partir de .

Cada registo no interior do ficheiro consiste numa `[LOG LEVEL]` linha de campos que contém um campo para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de registo é ERROR.

> [!NOTE]  
> Também pode gerar um ficheiro log4j utilizando a ferramenta de registo [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) e, em seguida, carregar esse ficheiro para a sua bolha. Consulte [os dados de upload para hDInsight](hdinsight-upload-data.md) para obter instruções. Para obter mais informações sobre como as bolhas no armazenamento do Azure são usadas com o HDInsight, consulte [Use Azure Blob Storage com HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Trabalho de exemplo

O seguinte trabalho pig `sample.log` latin carrega o ficheiro do armazenamento predefinido para o seu cluster HDInsight. Em seguida, executa uma série de transformações que resultam numa contagem de quantas vezes cada nível de registo ocorreu nos dados de entrada. Os resultados estão escritos para o STDOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

A imagem seguinte mostra um resumo do que cada transformação faz aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Executar o trabalho de Pig Latin

O HDInsight pode executar trabalhos de Pig Latin utilizando uma variedade de métodos. Use a tabela seguinte para decidir qual o método certo para si e, em seguida, siga o link para uma passagem.

## <a name="pig-and-sql-server-integration-services"></a>Serviços de Integração de Servidores de Porco e SQL

Pode utilizar os Serviços de Integração do Servidor SQL (SSIS) para executar um trabalho de Porco. O Pack de Recursos Azure para SSIS fornece os seguintes componentes que trabalham com trabalhos de porco no HDInsight.

* [Tarefa de porco Azure HDInsight][pigtask]

* [Gestor de conexão de assinatura Azure][connectionmanager]

Saiba mais sobre o Pacote de Funcionalidades Azure para O SSIS [aqui][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Passos seguintes

Agora que aprendeu a usar o Pig com o HDInsight, use os seguintes links para explorar outras formas de trabalhar com o Azure HDInsight.

* [Upload data to HDInsight (Carregar dados para o HDInsight)](hdinsight-upload-data.md)
* [Use a Colmeia Apache com HDInsight](./hadoop/hdinsight-use-hive.md)
* [Use Apache Sqoop com HDInsight](hdinsight-use-sqoop.md)
* [Utilizar tarefas de MapReduce com o HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
