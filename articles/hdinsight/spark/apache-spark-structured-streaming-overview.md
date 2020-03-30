---
title: Streaming estruturado de faíscas em Azure HDInsight
description: Como utilizar aplicações de streaming estruturadas de faíscas em clusters HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 19cfd5d8ed4100048c270fb41e5e54a920c61516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548841"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Visão geral do streaming estruturado de faíscas Apache

[Faísca Apache](https://spark.apache.org/) O Streaming Estruturado permite-lhe implementar aplicações escaláveis, de alta adesão e tolerantes a falhas para o processamento de fluxos de dados. O Streaming Estruturado é construído sobre o motor Spark SQL, e melhora com as construções a partir de Quadros de Dados Spark SQL e Conjuntos de Dados para que possa escrever consultas de streaming da mesma forma que escreveria consultas de lote.  

As aplicações de streaming estruturadas funcionam em clusters HDInsight Spark e conectam-se a dados de streaming de [Apache Kafka](https://kafka.apache.org/), uma tomada TCP (para fins de depuração), Armazenamento Azure ou Armazenamento de Lago de Dados Azure. As duas últimas opções, que dependem de serviços de armazenamento externos, permitem-lhe observar novos ficheiros adicionados ao armazenamento e processar os seus conteúdos como se fossem transmitidos.

O Streaming Estruturado cria uma consulta de longa duração durante a qual aplica operações aos dados de entrada, tais como seleção, projeção, agregação, janela, e juntar o DataFrame de streaming com DataFrames de referência. Em seguida, produz os resultados para armazenamento de ficheiros (Blobs de Armazenamento Azure ou Armazenamento de Data Lake) ou para qualquer datastore utilizando código personalizado (como Base de Dados SQL ou Power BI). O Streaming Estruturado também fornece saída para a consola para depuração local, e para uma tabela em memória para que possa ver os dados gerados para depuração no HDInsight.

![Processamento de fluxo com HDInsight e Streaming Estruturado de Faíscas](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> O Spark Structured Streaming está a substituir o Spark Streaming (DStreams). Para a frente, o Streaming Estruturado receberá melhorias e manutenção, enquanto os DStreams estarão apenas em modo de manutenção. O Streaming Estruturado não é atualmente tão completo como o DStreams para as fontes e pias que suporta fora da caixa, por isso avalie os seus requisitos para escolher a opção de processamento de fluxo spark apropriada.

## <a name="streams-as-tables"></a>Riachos como mesas

O Spark Structured Streaming representa um fluxo de dados como uma tabela que não está limitada em profundidade, ou seja, a tabela continua a crescer à medida que novos dados chegam. Esta tabela de *entrada* é continuamente processada por uma consulta de longa duração, e os resultados enviados para uma tabela de *saída:*

![Conceito de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

No Streaming Estruturado, os dados chegam ao sistema e são imediatamente ingeridos numa tabela de entrada. Escreve consultas (utilizando as APIs dataFrame e Dataset) que realizam operações contra esta tabela de entrada. A saída de consulta dá outra tabela, a tabela de *resultados.* A tabela de resultados contém os resultados da sua consulta, a partir da qual extrai dados para uma datastore externa, uma base de dados relacional. O tempo de quando os dados são processados a partir da tabela de entrada é controlado pelo intervalo do *gatilho*. Por padrão, o intervalo do gatilho é zero, por isso o Streaming Estruturado tenta processar os dados assim que chega. Na prática, isto significa que assim que o Streaming Estruturado é feito processando a execução da consulta anterior, inicia outra execução de processamento contra quaisquer dados recentemente recebidos. Pode configurar o gatilho a ser executado num intervalo, de modo a que os dados de streaming sejam processados em lotes baseados no tempo.

Os dados nas tabelas de resultados podem conter apenas os dados que são novos desde a última vez que a consulta foi processada (*modo apêndice*), ou a tabela pode ser atualizada cada vez que houver novos dados, pelo que a tabela inclui todos os dados de saída desde o início da consulta de streaming *(modo completo).*

### <a name="append-mode"></a>Modo apêndice

No modo apêndice, apenas as linhas adicionadas à tabela de resultados desde a última consulta estão presentes na tabela de resultados e escritas para armazenamento externo. Por exemplo, a consulta mais simples apenas copia todos os dados da tabela de entrada para a tabela de resultados inalterada. Cada vez que um intervalo de gatilho decorre, os novos dados são processados e as linhas que representam esses novos dados aparecem na tabela de resultados.

Considere um cenário em que está a processar a telemetria a partir de sensores de temperatura, como um termóstato. Assuma que o primeiro gatilho processou um evento no momento 00:01 para o dispositivo 1 com uma leitura de temperatura de 95 graus. No primeiro gatilho da consulta, apenas a fila com o tempo 00:01 aparece na tabela de resultados. Às 00:02, quando chega outro evento, a única nova fila é a fila com o tempo 00:02 e assim a tabela de resultados conteria apenas aquela linha.

![Modo de apêndice de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Ao utilizar o modo apêndice, a sua consulta estaria a aplicar projeções (selecionando as colunas de que se preocupa), filtrando (escolhendo apenas linhas que correspondam a determinadas condições) ou juntando (aumentando os dados com dados de uma mesa de look estática). O modo apêndice facilita a pressão apenas dos novos dados relevantes para o armazenamento externo.

### <a name="complete-mode"></a>Modo completo

Considere o mesmo cenário, desta vez usando o modo completo. Em modo completo, toda a tabela de saída é atualizada em cada gatilho, pelo que a tabela inclui dados não apenas da mais recente execução do gatilho, mas de todas as execuções. Pode utilizar o modo completo para copiar os dados inalterados da tabela de entrada para a tabela de resultados. Em cada corrida desencadeada, as novas linhas de resultados aparecem juntamente com todas as linhas anteriores. A tabela de resultados da saída acabará por armazenar todos os dados recolhidos desde o início da consulta, e acabaria por ficar sem memória. O modo completo destina-se a ser utilizado com consultas agregadas que resumem de alguma forma os dados de entrada, pelo que em cada gatilho a tabela de resultados é atualizada com um novo resumo.

Assuma que até agora existem cinco segundos de dados já processados, e é hora de processar os dados pelo sexto segundo. A mesa de entrada tem eventos para as 00:01 e hora 00:03. O objetivo desta consulta de exemplo é dar a temperatura média do dispositivo a cada cinco segundos. A implementação desta consulta aplica um agregado que leva todos os valores que se enquadram em cada janela de 5 segundos, média da temperatura, e produz uma linha para a temperatura média sobre esse intervalo. No final da primeira janela de 5 segundos, há dois tuples: (00:01, 1, 95) e (00:03, 1, 98). Assim, para a janela 00:00-00:05 a agregação produz uma tuple com a temperatura média de 96,5 graus. Na próxima janela de 5 segundos, só há um ponto de dados no momento 00:06, então a temperatura média resultante é de 98 graus. Às 00:10, utilizando o modo completo, a tabela de resultados tem as linhas para ambas as janelas 00:00-00:05 e 00:05-00:10 porque a consulta produz todas as linhas agregadas, e não apenas as novas. Assim, a tabela de resultados continua a crescer à medida que novas janelas são adicionadas.

![Modo completo de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem todas as consultas que utilizam o modo completo farão com que a mesa cresça sem limites.  Considere no exemplo anterior que, em vez de calcular a temperatura por janela do tempo, a média em vez de identificação do dispositivo. A tabela de resultados contém um número fixo de linhas (uma por dispositivo) com a temperatura média do dispositivo em todos os pontos de dados recebidos desse dispositivo. À medida que são recebidas novas temperaturas, a tabela de resultados é atualizada para que as médias da tabela estejam sempre atuais.

## <a name="components-of-a-spark-structured-streaming-application"></a>Componentes de uma aplicação de streaming estruturado de faíscas

Uma simples consulta de exemplo pode resumir as leituras de temperatura por janelas de uma hora. Neste caso, os dados são armazenados em ficheiros JSON no Armazenamento Azure (anexado como o armazenamento predefinido para o cluster HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Estes ficheiros JSON são `temps` armazenados na subpasta por baixo do recipiente do cluster HDInsight.

### <a name="define-the-input-source"></a>Definir a fonte de entrada

Primeiro configure um DataFrame que descreva a origem dos dados e quaisquer configurações exigidas por essa fonte. Este exemplo extrai-se dos ficheiros JSON no Armazenamento Azure e aplica-lhes um esquema no momento da leitura.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Aplicar a consulta

Em seguida, aplique uma consulta que contenha as operações desejadas contra o Streaming DataFrame. Neste caso, uma agregação agrupa todas as linhas em janelas de 1 hora, e depois calcula as temperaturas mínimas, médias e máximas naquela janela de 1 hora.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Defina o lavatório de saída

Em seguida, defina o destino para as linhas que são adicionadas à tabela de resultados dentro de cada intervalo do gatilho. Este exemplo apenas produz todas as linhas `temps` para uma tabela de memória que você pode mais tarde consultar com SparkSQL. O modo de saída completo garante que todas as linhas para todas as janelas são sempre saídas.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Inicie a consulta

Inicie a consulta de streaming e corra até receber um sinal de rescisão.

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Ver os resultados

Enquanto a consulta está em execução, na mesma SparkSession, pode `temps` fazer uma consulta SparkSQL contra a tabela onde os resultados da consulta são armazenados.

    select * from temps

Esta consulta produz resultados semelhantes aos seguintes:

| janela |  min (temporário) | avg (temporário) | máximo (temporário) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Para mais detalhes sobre a API de Fluxo Estruturado de Faíscas, juntamente com as fontes de dados de entrada, operações e sumidouros de saída que suporta, consulte o Guia de Programação de Streaming Estruturado de [Faíscas Apache](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Checkpointing e registos de escrita à frente

Para fornecer resiliência e tolerância a falhas, o Streaming Estruturado baseia-se no *checkpoint para* garantir que o processamento de fluxo pode continuar ininterrupto, mesmo com falhas no nó. No HDInsight, a Spark cria pontos de verificação para armazenamento duradouro, seja no Armazenamento Azure ou no Armazenamento de Data Lake. Estes postos de controlo armazenam as informações de progresso sobre a consulta de streaming. Além disso, o Streaming Estruturado utiliza um registo escrito *à frente* (WAL). O WAL captura dados ingeridos que foram recebidos mas ainda não processados por uma consulta. Se ocorrer uma falha e o processamento for reiniciado a partir do WAL, quaisquer eventos recebidos da fonte não se perdem.

## <a name="deploying-spark-streaming-applications"></a>Implementação de aplicações de streaming de faíscas

Normalmente, constrói uma aplicação Spark Streaming localmente num ficheiro JAR e, em seguida, implanta-a para Spark on HDInsight copiando o ficheiro JAR para o armazenamento predefinido anexado ao seu cluster HDInsight. Pode iniciar a sua aplicação com as APIs [Apache Livy](https://livy.incubator.apache.org/) REST disponíveis no seu cluster através de uma operação POST. O corpo do POST inclui um documento JSON que fornece o caminho para o seu JAR, o nome da classe cujo método principal define e executa a aplicação de streaming, e opcionalmente os requisitos de recursos do trabalho (como o número de executores, memória e núcleos) , e quaisquer configurações de configuração que o seu código de aplicação exija.

![Implementação de uma aplicação spark streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O estado de todas as candidaturas também pode ser verificado com um pedido GET contra um ponto final LIVY. Por fim, pode encerrar um pedido de execução emitindo um pedido DELETE contra o ponto final livy. Para mais detalhes sobre a API LIVY, consulte [trabalhos remotos com Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de streaming estruturado apache spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Lançar trabalhos Apache Spark remotamente com Apache LIVY](apache-spark-livy-rest-interface.md)
