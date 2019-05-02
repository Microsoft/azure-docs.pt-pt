---
title: Utilizar Apache Pig - Azure HDInsight
description: Saiba como utilizar o Pig com o Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: c39c332055943f915471d0da18526f793d4d7a4a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705025"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Utilizar o Apache Pig com o Apache Hadoop no HDInsight

Aprenda a usar [Apache Pig](https://pig.apache.org/) com HDInsight.

Apache Pig é uma plataforma para criar programas para o Apache Hadoop ao utilizar uma linguagem de procedimento conhecida como *Pig Latin*. PIg é uma alternativa ao Java para criar *MapReduce* soluções, que é incluído com o Azure HDInsight. Utilize a tabela seguinte para detetar as várias formas, Pig, podem ser utilizadas com o HDInsight:

| **Utilize esta opção** se pretender que... | ... .an **interativo** shell | ... **batch** processamento | ... .with isso **sistema operativo do cluster** | ... .from isso **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (por agora) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a id="why"></a>Porquê utilizar o Apache Pig

Um dos desafios do processamento de dados ao utilizar o MapReduce no Hadoop é implementar a lógica de processamento com apenas um mapa e uma função de redução. Para processamento complexo, muitas vezes tem de dividir o processamento em várias operações de MapReduce que são encadeadas em conjunto para obter o resultado desejado.

PIg permite-lhe definir o processamento como uma série de transformações que os dados fluem através para produzir a saída desejada.

O idioma de Pig Latin permite-lhe descrever o fluxo de dados de entrada não processado, por meio de transformações de um ou mais, para produzir a saída desejada. PIg Latin programas siga esse padrão geral:

* **Carga**: Ler os dados sejam manipulados do sistema de arquivos.

* **Transformar**: Manipular os dados.

* **Informação do Estado ou armazenar**: Enviar dados para o ecrã ou armazene-os para processamento.

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

PIg Latin também suporta as funções definidas pelo utilizador (UDF), que permite invocar componentes externos que implementam a lógica que é difícil de modelar em Pig Latin.

Para obter mais informações sobre o Pig Latin, consulte [Pig Latin referência Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) e [2 de Manual de referência do Pig Latin](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

Para obter um exemplo de como utilizar UDFs com Pig, consulte os seguintes documentos:

* [Utilizar DataFu do Apache com Apache Pig no HDInsight](apache-hadoop-use-pig-datafu-udf.md) -DataFu é uma coleção de UDFs útil mantida pela Apache
* [Utilizar Python com o Apache Pig e Apache Hive no HDInsight](python-udf-hdinsight.md)
* [Utilize C# com o Apache Hive e Apache Pig no HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Dados de exemplo

HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados na `/example/data` e `/HdiSamples` diretórios. Esses diretórios são no armazenamento padrão para o seu cluster. O exemplo de Pig neste documento utiliza a *log4j* ficheiro `/example/data/sample.log`.

Cada registo dentro do arquivo consiste numa linha de campos que contém um `[LOG LEVEL]` campo para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de registo é o erro.

> [!NOTE]  
> Também pode gerar um ficheiro de log4j, utilizando o [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) ferramenta de registo e, em seguida, carregar esse ficheiro para o blob. Ver [carregar dados para o HDInsight](../hdinsight-upload-data.md) para obter instruções. Para obter mais informações sobre a utilização de blobs no armazenamento do Azure com o HDInsight, consulte [utilize armazenamento de Blobs do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Tarefa de exemplo

A tarefa Pig Latin seguinte carrega o `sample.log` ficheiro a partir do armazenamento de predefinido para o seu cluster do HDInsight. Em seguida, ele executa uma série de transformações que resulta numa contagem de quantas vezes cada nível de registo ocorreu nos dados de entrada. Os resultados são escritos para STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

A imagem seguinte mostra um resumo do que faz cada transformação para os dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

## <a id="run"></a>Executar a tarefa Pig Latin

HDInsight pode executar tarefas do Pig Latin, usando uma variedade de métodos. Utilize a tabela seguinte para decidir qual é o método adequado para si, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção** se pretender que... | ... .an **interativo** shell | ... **batch** processamento | ... .with isso **sistema operativo do cluster** | ... .from isso **cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (por agora) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="pig-and-sql-server-integration-services"></a>PIg e SQL Server Integration Services

Pode usar o SQL Server Integration Services (SSIS) para executar uma tarefa Pig. O pacote de funcionalidades do Azure para SSIS fornece os seguintes componentes que funcionam com tarefas do Pig no HDInsight.

* [Tarefas do Pig do HDInsight do Azure][pigtask]

* [Gestor de ligação de subscrição do Azure][connectionmanager]

Saiba mais sobre o pacote de recursos do Azure para SSIS [aqui][ssispack].

## <a id="nextsteps"></a>Passos seguintes
Agora que aprendeu como utilizar o Pig com o HDInsight, utilize as seguintes ligações para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados para o HDInsight](../hdinsight-upload-data.md)
* [Utilizar o Apache Hive com o HDInsight][hdinsight-use-hive]
* [Utilizar o Apache Sqoop com o HDInsight](hdinsight-use-sqoop.md)
* [Utilizar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [Utilizar tarefas de MapReduce com o HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
