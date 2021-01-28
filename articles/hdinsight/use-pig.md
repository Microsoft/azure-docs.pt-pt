---
title: Utilizar Apache Pig
titleSuffix: Azure HDInsight
description: Aprenda a usar o Pig com Apache Hadoop em HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944611"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Use o Porco Apache com Apache Hadoop em HDInsight

Aprenda a usar [o Apache Pig](https://pig.apache.org/) com HDInsight.

Apache Pig é uma plataforma para criar programas para Apache Hadoop usando uma linguagem processual conhecida como *Pig Latin.* O porco é uma alternativa a Java para criar soluções *MapReduce,* e está incluído com Azure HDInsight. Use a seguinte tabela para descobrir as várias formas de o Porco ser usado com HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Por que usar o Porco Apache

Um dos desafios do processamento de dados usando o MapReduce em Hadoop é implementar a sua lógica de processamento usando apenas um mapa e uma função de redução. Para um processamento complexo, você muitas vezes tem que quebrar o processamento em várias operações MapReduce que estão acorrentadas em conjunto para alcançar o resultado desejado.

O porco permite definir o processamento como uma série de transformações que os dados fluem para produzir a produção desejada.

A língua pig latina permite-lhe descrever o fluxo de dados da entrada bruta, através de uma ou mais transformações, para produzir a produção desejada. Os programas de pig latin seguem este padrão geral:

* **Carga:** Leia os dados a manipular a partir do sistema de ficheiros.

* **Transformar**: Manipular os dados.

* **Despeje ou guarde:** Desajeição de dados para o ecrã ou guarde-os para processamento.

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

O Pig Latin também suporta funções definidas pelo utilizador (UDF), que permite invocar componentes externos que implementam uma lógica difícil de modelar em Pig Latin.

Para obter mais informações sobre o pig latin, consulte [o Manual de Referência Latino do Porco 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) e o Manual de Referência Latina do Porco [2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados nos `/example/data` `/HdiSamples` diretórios e nos diretórios. Estes diretórios estão no armazenamento padrão para o seu cluster. O exemplo de Porco neste documento utiliza o ficheiro *log4j* de `/example/data/sample.log` .

Cada registo dentro do ficheiro consiste numa linha de campos que contém um `[LOG LEVEL]` campo para mostrar o tipo e a gravidade, por exemplo:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

No exemplo anterior, o nível de registo é ERROR.

> [!NOTE]  
> Também pode gerar um ficheiro log4j utilizando a ferramenta de registo [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) e, em seguida, fazer o upload desse ficheiro para a sua bolha. Consulte [os dados do upload para o HDInsight](hdinsight-upload-data.md) para obter instruções. Para obter mais informações sobre como as bolhas no armazenamento Azure são usadas com HDInsight, consulte [Use Azure Blob Storage com HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Exemplo de trabalho

O seguinte trabalho pig latin carrega o `sample.log` ficheiro do armazenamento predefinido para o seu cluster HDInsight. Em seguida, executa uma série de transformações que resultam numa contagem de quantas vezes cada nível de registo ocorreu nos dados de entrada. Os resultados são escritos para STDOUT.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

A imagem a seguir mostra um resumo do que cada transformação faz aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Executar o trabalho de porco latino

HDInsight pode executar trabalhos latinos de porco usando uma variedade de métodos. Use a seguinte tabela para decidir qual o método certo para si e, em seguida, siga o link para uma passagem.

## <a name="pig-and-sql-server-integration-services"></a>Serviços de Integração de Servidores de Porco e SQL

Você pode usar os Serviços de Integração de Servidor SQL (SSIS) para executar um trabalho de Porco. O Azure Feature Pack for SSIS fornece os seguintes componentes que trabalham com trabalhos de porco em HDInsight.

* [Tarefa do porco Azure HDInsight][pigtask]

* [Gestor de conexão de assinatura Azure][connectionmanager]

Saiba mais sobre o Azure Feature Pack para SSIS [aqui.][ssispack]

## <a name="next-steps"></a><a id="nextsteps"></a>Próximos passos

Agora que aprendeu a usar o Pig com HDInsight, use os seguintes links para explorar outras formas de trabalhar com a Azure HDInsight.

* [Upload data to HDInsight (Carregar dados para o HDInsight)](hdinsight-upload-data.md)
* [Use a Colmeia Apache com HDInsight](./hadoop/hdinsight-use-hive.md)
* [Use Apache Sqoop com HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [Utilizar tarefas de MapReduce com o HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif