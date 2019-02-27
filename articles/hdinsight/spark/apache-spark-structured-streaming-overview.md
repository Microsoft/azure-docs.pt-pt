---
title: Spark Structured Streaming no Azure HDInsight
description: Como utilizar aplicações de transmissão estruturada do Spark nos clusters do Spark do HDInsight.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: 8c37093b5369d2390942bc00c1531248bc4424c8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869093"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Descrição geral do Apache Spark transmissão em fluxo estruturada

[Apache Spark](https://spark.apache.org/) transmissão em fluxo estruturada permite-lhe implementar aplicações dimensionáveis e de alto débito, tolerante a falhas para o processamento de fluxos de dados. A transmissão em fluxo estruturada foi criada sobre o mecanismo de Spark SQL e aprimora as construções de quadros de dados do Spark SQL e conjuntos de dados para que pode escrever a transmissão em fluxo consulta da mesma forma que deve escrever consultas em lote.  

Aplicações de transmissão em fluxo estruturadas serem executadas em clusters do HDInsight Spark e ligar a dados a partir de transmissão em fluxo [Apache Kafka](https://kafka.apache.org/), um soquete (para fins de depuração) TCP, o armazenamento do Azure ou o armazenamento do Azure Data Lake. As duas últimas opções, que dependem de serviços de armazenamento externo, permitem-lhe ver para novos ficheiros adicionados para o armazenamento e processar seu conteúdo como se eles foram transmitidos em fluxo. 

A transmissão em fluxo estruturada cria uma consulta de execução longa durante o qual aplicar operações para os dados de entrada, como seleção, projeção, agregação, janelas e associar o pacote de dados de transmissão em fluxo com referência DataFrames. Em seguida, enviar os resultados para armazenamento de ficheiros (Blobs de armazenamento do Azure ou armazenamento do Data Lake) ou para qualquer arquivo de dados com o código personalizado (por exemplo, a base de dados SQL ou o Power BI). A transmissão em fluxo estruturada também fornece a saída para a consola de depuração localmente e para uma tabela em memória para que possa ver os dados gerados para depuração no HDInsight. 

![Transmissão em fluxo estruturada de Stream processar com o HDInsight e o Spark](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> A transmissão estruturada do Spark é substituir o Spark Streaming (DStreams). Daqui em diante, transmissão em fluxo estruturada receberá aprimoramentos e manutenção, enquanto DStreams estará no modo de manutenção apenas. A transmissão em fluxo estruturada não está atualmente como recursos completos como DStreams para as origens e sinks que ele oferece suporte de imediato, portanto, avalie os requisitos para escolher o Spark apropriado a opção de processamento de fluxo. 

## <a name="streams-as-tables"></a>Fluxos como tabelas

A transmissão estruturada do Spark representa um fluxo de dados como uma tabela que é não vinculada em detalhes, ou seja, a tabela continua a crescer à medida que chegam novos dados. Isso *tabela de entrada* continuamente é processado por uma consulta de execução longa e os resultados enviados para um *tabela de saída*:

![Conceito de transmissão em fluxo estruturada](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Na transmissão em fluxo estruturada, os dados chegam o sistema e imediatamente são ingeridos para uma tabela de entrada. Escrever consultas (usando o pacote de dados e APIs de conjunto de dados) que executam operações em relação a esta tabela de entrada. O resultado da consulta resulta em outra tabela, o *tabela de resultados*. A tabela de resultados contém os resultados da sua consulta, a partir do qual desenha dados para um arquivo de dados externo, essa base de dados relacional. A temporização de quando os dados são processados da tabela de entrada é controlada pelos *intervalo de Acionador*. Por predefinição, o intervalo de Acionador é zero, para que a transmissão em fluxo estruturada tenta processar os dados assim que são recebidos. Na prática, isso significa que assim que a transmissão em fluxo estruturada é feita a execução da consulta anterior de processamento, ele começa outro processamento executado em todos os dados recebidos recentemente. Pode configurar o acionador para ser executado num intervalo, para que os dados de transmissão em fluxo são processados em lotes com base no tempo. 

Os dados nos resultados de tabelas podem conter apenas os dados que há de novos desde o último tempo a consulta foi processados (*acrescentar modo*), ou a tabela pode ser completamente atualizada sempre que houver novos dados, para a tabela inclui todos os dados de saída uma vez que começou a consulta de transmissão em fluxo (*modo de conclusão*).

### <a name="append-mode"></a>Modo de acrescentar

Em acréscimo modo, apenas as linhas que foram adicionados à tabela de resultados, uma vez que a última execução de consulta estão presentes na tabela de resultados e escritos no armazenamento externo. Por exemplo, a consulta mais simples apenas copia todos os dados da tabela de entrada para a tabela de resultados inalterada. Sempre que um intervalo de Acionador decorrido, os novos dados são processados e as linhas que representam os novos dados são apresentados na tabela de resultados. 

Considere um cenário em que estão a processar telemetria de sensores de temperatura, por exemplo, um termóstato. Suponha que o primeiro acionador processados um evento no tempo 00:01, para o dispositivo 1 com uma leitura de temperatura de 95 graus. O primeiro acionador da consulta, apenas a linha com tempo 00:01 aparece na tabela de resultados. No tempo 00:02 quando outro evento é recebido, apenas nova linha é a linha com a hora 00:02 e por isso, a tabela de resultados conteria apenas que uma linha.

![Transmissão em fluxo estruturada acrescentar modo](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Quando utilizar acrescentar modo, a consulta poderiam ser projeções aplicar (selecionar as colunas que ele se preocupa), filtragem (apenas as linhas que correspondem a determinadas condições de separação) ou ao associar (aumentar os dados com dados de uma tabela de pesquisa estática). Acrescente faz de modo a enviar apenas os novos dados relevantes aponta para armazenamento externo.

### <a name="complete-mode"></a>Modo de conclusão

Considere o mesmo cenário, desta vez usando o modo de conclusão. No modo de conclusão, é atualizar a tabela de saída completa em cada acionador para que a tabela inclui não apenas a partir da execução do acionador mais recente, mas a partir de todas as execuções de dados. Poderia usar o modo de completado para copiar os dados inalterados da tabela de entrada para a tabela de resultados. Em todas as execuções acionadas, as novas linhas de resultado são apresentados juntamente com todas as linhas anteriores. A tabela de resultados de saída acabará armazenando todos os dados recolhidos, uma vez que começou a consulta e, eventualmente, deverá executar a memória esgotada. Modo completado destina-se a utilização com as consultas de agregação que resumem os dados de entrada de alguma forma, e assim por diante, cada acionador a tabela de resultados é atualizada com um resumo de novo. 

Pressupõem até agora, há o valor de cinco segundos de dados já processados e chegou a hora para processar os dados para a segunda a sexta. A tabela de entrada tem eventos para o tempo 00:01 e 00:03 de tempo. O objetivo desta consulta de exemplo é dar a temperatura média do dispositivo a cada cinco segundos. A implementação desta consulta aplica-se um agregado que aproveita todos os valores que se inserem num cada janela de 5 segundos, calcula a média de temperatura e produz uma linha para a temperatura média ao longo desse intervalo. No final da primeira janela de 5 segundos, há duas tuplas: (00:01, 1, 95) e (00:03, 1, 98). Então, para que a janela das 00:00-00:05 a agregação produz uma cadeia de identificação com a temperatura média de 96.5 graus. Na próxima janela de 5 segundos, existe apenas um ponto de dados em tempo 00:06, pelo que a temperatura média resultante é 98 graus. No tempo 00:10, utilizar o modo de conclusão, a tabela de resultados tem as linhas para ambas as 00:00 de windows-00:05 e 00:05 00:10 porque a consulta devolve todas as linhas de agregados, não apenas as novas. Portanto, a tabela de resultados continua a crescer à medida que são adicionadas novas janelas.    

![Modo de conclusão de transmissão em fluxo estruturada](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem todas as consultas com o modo de conclusão fará com que a tabela crescer sem limites.  Considere o exemplo anterior, que em vez de uma média de temperatura por janela de tempo, ele apresentou uma média em vez disso, por ID de dispositivo. Tabela de resultados contém um número fixo de linhas (um por dispositivo) com a temperatura média para o dispositivo em todos os pontos de dados recebidos a partir desse dispositivo. À medida que são recebidas novas temperaturas, a tabela de resultados é atualizada para que as médias na tabela estejam sempre atuais. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Componentes de uma aplicação de transmissão estruturada do Spark

Uma consulta de exemplo simples pode resumir as leituras de temperatura pelo hour-long windows. Neste caso, os dados são armazenados em ficheiros JSON no armazenamento do Azure (anexado como armazenamento predefinido para o cluster do HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Estes ficheiros JSON são armazenados no `temps` subpasta por baixo do contentor do cluster HDInsight. 

### <a name="define-the-input-source"></a>Definir a origem de entrada

Primeiro de configurar um DataFrame que descreve a origem de dados e definições necessárias para essa origem. Este exemplo desenha a partir dos ficheiros JSON no armazenamento do Azure e aplica-se um esquema a eles em tempo de leitura.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Aplicam-se a consulta

Em seguida, aplicam-se uma consulta que contém as operações pretendidas contra o pacote de dados de transmissão em fluxo. Neste caso, uma agregação agrupa todas as linhas em windows de 1 hora e, em seguida, calcula as temperaturas máxima, mínimas e médios nessa janela de 1 hora.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definir o sink de saída

Em seguida, defina o destino para as linhas que são adicionados à tabela de resultados dentro de cada intervalo de Acionador. Neste exemplo produz apenas todas as linhas a uma tabela em memória `temps` que pode consultar mais tarde com SparkSQL. Modo de saída completa assegura que todas as linhas para todos os windows são os saída sempre.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Iniciar a consulta

Inicie a consulta de transmissão em fluxo e execute até que é recebido um sinal de terminação. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Ver os resultados

Enquanto a consulta está em execução, no SparkSession mesmo, pode executar uma consulta de SparkSQL em relação a `temps` onde estão armazenados os resultados da consulta de tabela. 

    select * from temps

Essa consulta produz resultados semelhantes ao seguinte:


| janela |  min(temp) | avg(temp) | max(temp) |
| --- | --- | --- | --- |
|{u'start': ' 2016 do u-07-26T02:00:00.000Z', u'end "... |    95 |    95.231579 | 99 |
|{u'start': ' 2016 do u-07-26T03:00:00.000Z', u'end "...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': ' 2016 do u-07-26T05:00:00.000Z', u'end "...  |95 |   96.984639 | 99 |
|{u'start': ' 2016 do u-07-26T06:00:00.000Z', u'end "...  |95 |   97.014749 | 99 |
|{u'start': ' 2016 do u-07-26T07:00:00.000Z', u'end "...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Para obter detalhes sobre a API de Stream estruturada do Spark, juntamente com os dados de entrada origens, as operações e saída Coletores de ele suporta, consulte [Apache Spark Structured Streaming guia de programação](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Pontos de verificação e registos de escrita-ahead

Para fornecer resiliência e tolerância a falhas, transmissão em fluxo estruturada depende *ponto de verificação* para esse fluxo de garantir o processamento poder continuar sem interrupções, mesmo com falhas de nó. No HDInsight, Spark cria pontos de verificação para o armazenamento durável, de armazenamento do Azure ou de armazenamento do Data Lake. Estes pontos de verificação armazenam as informações de progresso sobre a consulta de transmissão em fluxo. Além disso, transmissão em fluxo estruturada utiliza um *registo de escrita-ahead* (WAL). O WAL captura dados ingeridos que foi recebidos, mas ainda não processados por uma consulta. Se ocorrer uma falha e o processamento é reiniciado desde o WAL, nenhum evento recebido da origem não é perdido.

## <a name="deploying-spark-streaming-applications"></a>Implementação de aplicações de transmissão em fluxo do Spark

Normalmente, criar uma aplicação de transmissão em fluxo do Spark de mensagens em fila localmente num ficheiro JAR e, em seguida, implementá-la ao Spark no HDInsight ao copiar o ficheiro JAR para o armazenamento de predefinido ligado ao seu cluster do HDInsight. Pode começar a sua aplicação com o [Apache Livy](https://livy.incubator.apache.org/) disponível a partir do seu cluster através de uma operação de publicação de APIs REST. O corpo da POSTAGEM inclui um documento JSON que fornece o caminho para o JAR, o nome da classe cujo principal método define e executa a aplicação de transmissão em fluxo e, opcionalmente, os requisitos de recursos da tarefa (por exemplo, o número de executor, memória e núcleos) , e as definições de configuração requer que o código da aplicação.

![Implantando um aplicativo de transmissão em fluxo do Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Também pode ser verificado o estado de todos os aplicativos com um pedido GET relativamente um ponto de extremidade do LIVY. Por fim, pode terminar um aplicativo em execução ao emitir um pedido de eliminação relativamente ao ponto de final do LIVY. Para obter detalhes sobre a API do LIVY, consulte [tarefas remotas com Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos Seguintes

* [Criar um cluster do Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de transmissão em fluxo estruturada do Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Iniciar tarefas do Apache Spark remotamente com o Apache LIVY](apache-spark-livy-rest-interface.md)
