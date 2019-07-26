---
title: Streaming estruturado do Spark no Azure HDInsight
description: Como usar aplicativos de streaming estruturado do Spark em clusters do HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: hrasheed
ms.openlocfilehash: 0aaca127fec82d35da0ba943e97221834c2e42ed
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441901"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Visão geral do Apache Spark streaming estruturado

[Apache Spark](https://spark.apache.org/) A transmissão estruturada permite que você implemente aplicativos escalonáveis, de alta taxa de transferência e tolerante a falhas para processar fluxos de dados. O streaming estruturado é criado com base no mecanismo do Spark SQL e melhora as construções de quadros de dados e conjuntos do Spark SQL para que você possa escrever consultas de streaming da mesma maneira que escreveria consultas em lote.  

Os aplicativos de streaming estruturados são executados em clusters Spark do HDInsight e se conectam a dados de streaming de [Apache Kafka](https://kafka.apache.org/), um soquete TCP (para fins de depuração), armazenamento do Azure ou Azure data Lake Storage. As duas últimas opções, que dependem de serviços de armazenamento externo, permitem que você assista a novos arquivos adicionados ao armazenamento e processe seu conteúdo como se eles estivessem em fluxo. 

O streaming estruturado cria uma consulta de execução longa durante a qual você aplica operações aos dados de entrada, como seleção, projeção, agregação, janelas e junção do dataframe de streaming com quadros de dados de referência. Em seguida, você produz os resultados no armazenamento de arquivos (BLOBs de armazenamento do Azure ou Data Lake Storage) ou em qualquer armazenamento de dados usando código personalizado (como o banco de dados SQL ou Power BI). A transmissão estruturada também fornece saída para o console para depuração local e para uma tabela na memória para que você possa ver os dados gerados para depuração no HDInsight. 

![Processamento de fluxo com o HDInsight e o streaming estruturado do Spark](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> O streaming estruturado do Spark está substituindo o streaming do Spark (DStreams). No futuro, a transmissão estruturada receberá melhorias e manutenção, enquanto o DStreams estará apenas no modo de manutenção. No momento, o streaming estruturado não é tão completo quanto o recurso de DStreams para as fontes e coletores que ele dá suporte pronto para uso, portanto, avalie seus requisitos para escolher a opção de processamento de fluxo Spark apropriada. 

## <a name="streams-as-tables"></a>Fluxos como tabelas

O streaming estruturado do Spark representa um fluxo de dados como uma tabela que não está vinculada em profundidade, ou seja, a tabela continua crescendo à medida que novos dados chegam. Essa *tabela de entrada* é processada continuamente por uma consulta de execução longa e os resultados são enviados a uma *tabela de saída*:

![Conceito de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

No streaming estruturado, os dados chegam ao sistema e são imediatamente incluídos em uma tabela de entrada. Você escreve consultas (usando as APIs dataframe e DataSet) que executam operações nessa tabela de entrada. A saída da consulta produz outra tabela, a *tabela de resultados*. A tabela de resultados contém os resultados da consulta, a partir da qual você desenha dados para um repositório de armazenamento externo, como um banco de dado relacional. O tempo de quando os dados são processados da tabela de entrada é controlado pelo *intervalo de gatilho*. Por padrão, o intervalo de gatilho é zero, portanto, o streaming estruturado tenta processar os dados assim que eles chegam. Na prática, isso significa que assim que o streaming estruturado terminar de processar a execução da consulta anterior, ele iniciará outra execução de processamento em relação a quaisquer dados recebidos recentemente. Você pode configurar o gatilho para ser executado em um intervalo, para que os dados de streaming sejam processados em lotes baseados em tempo. 

Os dados nas tabelas de resultados podem conter apenas os dados que são novos desde a última vez em que a consulta foi processada (*modo de acréscimo*) ou a tabela pode ser completamente atualizada sempre que houver novos dados para que a tabela inclua todos os dados de saída desde o streaming q Ulta iniciado (*modo completo*).

### <a name="append-mode"></a>Modo de acrescentar

No modo de acréscimo, somente as linhas adicionadas à tabela de resultados desde a última execução de consulta estão presentes na tabela de resultados e gravadas no armazenamento externo. Por exemplo, a consulta mais simples apenas copia todos os dados da tabela de entrada para a tabela de resultados inalterada. Cada vez que um intervalo de gatilho expira, os novos dados são processados e as linhas que representam os novos dados aparecem na tabela de resultados. 

Considere um cenário em que você está processando a telemetria de sensores de temperatura, como um termostato. Suponha que o primeiro gatilho tenha processado um evento no momento 00:01 para o dispositivo 1 com uma leitura de temperatura de 95 graus. No primeiro gatilho da consulta, somente a linha com a hora 00:01 aparece na tabela de resultados. No horário 00:02 quando outro evento chega, a única nova linha é a linha com tempo 00:02 e, portanto, a tabela de resultados conterá apenas essa linha.

![Modo de acréscimo de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Ao usar o modo de acréscimo, sua consulta estaria aplicando projeções (selecionando as colunas sobre as quais ele se preocupa), filtrando (selecionando apenas as linhas que correspondem a determinadas condições) ou unindo (aumentando os dados com dados de uma tabela de pesquisa estática). O modo de acréscimo facilita o envio de pontos de dados novos relevantes para o armazenamento externo.

### <a name="complete-mode"></a>Modo completo

Considere o mesmo cenário, desta vez usando o modo completo. No modo completo, toda a tabela de saída é atualizada em todos os gatilhos para que a tabela inclua dados não apenas da execução do gatilho mais recente, mas de todas as execuções. Você pode usar o modo completo para copiar os dados inalterados da tabela de entrada para a tabela de resultados. Em cada execução disparada, as novas linhas de resultado aparecem junto com todas as linhas anteriores. A tabela de resultados de saída acabará armazenando todos os dados coletados desde o início da consulta e, eventualmente, você ficará sem memória. O modo completo destina-se ao uso com consultas de agregação que resumem os dados de entrada de alguma forma e, assim, em cada gatilho, a tabela de resultados é atualizada com um novo Resumo. 

Suponha que até agora há cinco segundos de dados já processados, e é hora de processar os dados para o sexto segundo. A tabela de entrada tem eventos para o tempo 00:01 e o tempo 00:03. O objetivo desta consulta de exemplo é fornecer a temperatura média do dispositivo a cada cinco segundos. A implementação dessa consulta aplica uma agregação que pega todos os valores que se enquadram em cada janela de 5 segundos, calcula a temperatura e produz uma linha para a temperatura média sobre esse intervalo. No final da primeira janela de 5 segundos, há duas tuplas: (00:01, 1, 95) e (00:03, 1, 98). Assim, para a janela 00:00-00:05, a agregação produz uma tupla com a temperatura média de 96,5 graus. Na próxima janela de 5 segundos, há apenas um ponto de dados no momento 00:06, portanto, a temperatura média resultante é de 98 graus. No momento 00:10, usando o modo completo, a tabela de resultados tem as linhas para o Windows 00:00-00:05 e o 00:05-00:10 porque a consulta gera todas as linhas agregadas, não apenas as novas. Portanto, a tabela de resultados continua crescendo conforme novas janelas são adicionadas.    

![Modo de conclusão de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem todas as consultas que usam o modo completo farão com que a tabela cresça sem limites.  Considere no exemplo anterior que, em vez de calcular a temperatura por janela de tempo, ela é calculada por meio da ID do dispositivo. A tabela de resultados contém um número fixo de linhas (um por dispositivo) com a temperatura média para o dispositivo em todos os pontos de dados recebidos desse dispositivo. À medida que novas temperaturas são recebidas, a tabela de resultados é atualizada para que as médias na tabela sejam sempre atuais. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Componentes de um aplicativo de streaming estruturado do Spark

Uma consulta de exemplo simples pode resumir as leituras de temperatura por janelas de duração de hora. Nesse caso, os dados são armazenados em arquivos JSON no armazenamento do Azure (anexado como o armazenamento padrão para o cluster HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Esses arquivos JSON são armazenados na `temps` subpasta sob o contêiner do cluster HDInsight. 

### <a name="define-the-input-source"></a>Definir a fonte de entrada

Primeiro, configure um dataframe que descreva a origem dos dados e as configurações necessárias para essa fonte. Este exemplo se baseia nos arquivos JSON no armazenamento do Azure e aplica um esquema a eles em tempo de leitura.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Aplicar a consulta

Em seguida, aplique uma consulta que contenha as operações desejadas no dataframe de streaming. Nesse caso, uma agregação agrupa todas as linhas em janelas de 1 hora e, em seguida, computa as temperaturas mínima, média e máxima nessa janela de 1 hora.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definir o coletor de saída

Em seguida, defina o destino para as linhas que são adicionadas à tabela de resultados dentro de cada intervalo de gatilho. Este exemplo apenas gera todas as linhas para uma tabela `temps` na memória que você pode consultar posteriormente com SparkSQL. O modo de saída completo garante que todas as linhas de todas as janelas sejam geradas todas as vezes.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Iniciar a consulta

Inicie a consulta de streaming e execute até que um sinal de encerramento seja recebido. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Ver os resultados

Enquanto a consulta está em execução, no mesmo SparkSession, você pode executar uma consulta `temps` SparkSQL na tabela em que os resultados da consulta são armazenados. 

    select * from temps

Essa consulta produz resultados semelhantes ao seguinte:


| Window |  min(temp) | avg(temp) | Max (Temp) |
| --- | --- | --- | --- |
|{u'start ': u ' 2016-07-26T02:00:00.000 Z ', u'end '... |    95 |    95,231579 | 99 |
|{u'start ': u ' 2016-07-26T03:00:00.000 Z ', u'end '...  |95 |   96.023048 | 99 |
|{u'start ': u ' 2016-07-26T04:00:00.000 Z ', u'end '...  |95 |   96,797133 | 99 |
|{u'start ': u ' 2016-07-26T05:00:00.000 Z ', u'end '...  |95 |   96,984639 | 99 |
|{u'start ': u ' 2016-07-26T06:00:00.000 Z ', u'end '...  |95 |   97, 14749 | 99 |
|{u'start ': u ' 2016-07-26T07:00:00.000 Z ', u'end '...  |95 |   96.980971 | 99 |
|{u'start ': u ' 2016-07-26T08:00:00.000 Z ', u'end '...  |95 |   96,965997 | 99 |  

Para obter detalhes sobre a API de fluxo estruturado do Spark, junto com as fontes de dados de entrada, operações e coletores de saída aos quais ele dá suporte, consulte [Apache Spark guia de programação de streaming estruturado](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Logs de ponto de verificação e write-ahead

Para fornecer resiliência e tolerância a falhas, o streaming estruturado depende do *ponto de verificação* para garantir que o processamento de fluxo possa continuar sem interrupções, mesmo com falhas de nó. No HDInsight, o Spark cria pontos de verificação para o armazenamento durável, o armazenamento do Azure ou o Data Lake Storage. Esses pontos de verificação armazenam as informações de progresso sobre a consulta de streaming. Além disso, o streaming estruturado usa um *log write-ahead* (WAL). O WAL captura dados ingeridos que foram recebidos, mas que ainda não foram processados por uma consulta. Se ocorrer uma falha e o processamento for reiniciado a partir de WAL, todos os eventos recebidos da origem não serão perdidos.

## <a name="deploying-spark-streaming-applications"></a>Implantando aplicativos Spark streaming

Normalmente, você cria um aplicativo Spark streaming localmente em um arquivo JAR e, em seguida, implanta-o no Spark no HDInsight copiando o arquivo JAR para o armazenamento padrão anexado ao seu cluster HDInsight. Você pode iniciar seu aplicativo com as APIs REST do [Apache Livy](https://livy.incubator.apache.org/) disponíveis no cluster usando uma operação post. O corpo da POSTAgem inclui um documento JSON que fornece o caminho para o JAR, o nome da classe cujo método principal define e executa o aplicativo de streaming e, opcionalmente, os requisitos de recurso do trabalho (como o número de executores, memória e núcleos) e quaisquer definições de configuração que o código do aplicativo exigir.

![Implantando um aplicativo Spark streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O status de todos os aplicativos também pode ser verificado com uma solicitação GET em relação a um ponto de extremidade LIVY. Por fim, você pode encerrar um aplicativo em execução emitindo uma solicitação de exclusão no ponto de extremidade LIVY. Para obter detalhes sobre a API do LIVY, consulte [trabalhos remotos com o Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos Seguintes

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de Apache Spark de streaming estruturado](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Iniciar Apache Spark trabalhos remotamente com o Apache LIVY](apache-spark-livy-rest-interface.md)
