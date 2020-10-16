---
title: Fluxo estruturado de faísca em Azure HDInsight
description: Como utilizar aplicações de streaming estruturadas de faíscas em clusters HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 46a65720c9998a7a56d0ca269c344f85c5955546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086148"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Visão geral do Fluxo Estruturado de Faíscas Apache Spark

[Faísca Apache](https://spark.apache.org/) O Streaming Estruturado permite-lhe implementar aplicações escaláveis, de alta produção e tolerantes a falhas para o processamento de fluxos de dados. O Streaming Estruturado é construído sobre o motor Spark SQL, e melhora as construções a partir de Spark SQL Data Frames e Datasets para que possa escrever consultas de streaming da mesma forma que escreveria consultas de lote.  

As aplicações de streaming estruturadas funcionam em clusters HDInsight Spark e conectam-se a dados de streaming da [Apache Kafka](https://kafka.apache.org/), uma tomada TCP (para fins de depuração), armazenamento de Azure ou Armazenamento do Lago de Dados Azure. As duas últimas opções, que dependem de serviços de armazenamento externos, permitem-lhe observar novos ficheiros adicionados ao armazenamento e processar os seus conteúdos como se fossem transmitidos.

O Streaming Estruturado cria uma consulta de longa duração durante a qual aplica operações aos dados de entrada, tais como seleção, projeção, agregação, janela e unição do DataFrame de streaming com dataframes de referência. Em seguida, produz os resultados para o armazenamento de ficheiros (Azure Storage Blobs ou Data Lake Storage) ou para qualquer datastore utilizando código personalizado (como SQL Database ou Power BI). O Streaming Estruturado também fornece saída para a consola para depuração local, e para uma tabela de memória para que possa ver os dados gerados para depuração em HDInsight.

![Processamento de fluxo com HDInsight e Streaming Estruturado de Faíscas](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> O Streaming Estruturado de Faísca está a substituir o Streaming spark (DStreams). Para a frente, o Streaming Estruturado receberá melhorias e manutenção, enquanto o DStreams estará apenas em modo de manutenção. O Streaming Estruturado não é atualmente tão completo como dStreams para as fontes e pias que suporta fora da caixa, por isso, avalie os seus requisitos para escolher a opção de processamento de fluxo spark apropriado.

## <a name="streams-as-tables"></a>Riachos como tabelas

O Spark Structured Streaming representa um fluxo de dados como uma tabela que não é limitada em profundidade, ou seja, a tabela continua a crescer à medida que novos dados chegam. Esta *tabela de entrada* é continuamente processada por uma consulta de longa duração, e os resultados enviados para uma tabela de *saída:*

![Conceito de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

No Streaming Estruturado, os dados chegam ao sistema e são imediatamente ingeridos numa tabela de entrada. Escreve consultas (utilizando as APIs do DataFrame e do Dataset) que realizam operações contra esta tabela de entrada. A produção de consulta produz outra tabela, a *tabela de resultados.* A tabela de resultados contém os resultados da sua consulta, a partir da qual se extraem dados para uma data-loja externa, tal base de dados relacional. O tempo de quando os dados são tratados a partir da tabela de entrada é controlado pelo *intervalo de disparo*. Por predefinição, o intervalo do gatilho é zero, pelo que o Streaming Estruturado tenta processar os dados assim que chega. Na prática, isto significa que assim que o Streaming Estruturado é feito o processamento da execução da consulta anterior, inicia outra execução de processamento contra quaisquer dados recém-recebidos. Pode configurar o gatilho para ser executado num intervalo, de modo a que os dados de streaming são processados em lotes baseados no tempo.

Os dados nas tabelas de resultados podem conter apenas os dados que são novos desde a última vez que a consulta foi processada (*modo de apêndice),* ou a tabela pode ser atualizada sempre que há novos dados, pelo que a tabela inclui todos os dados de saída desde o início da consulta de streaming *(modo completo).*

### <a name="append-mode"></a>Modo apêndice

No modo de apêndice, apenas as linhas adicionadas à tabela de resultados desde a última consulta estão presentes na tabela de resultados e escritas para armazenamento externo. Por exemplo, a consulta mais simples apenas copia todos os dados da tabela de entrada para a tabela de resultados inalterada. Cada vez que decorre um intervalo de gatilho, os novos dados são tratados e as linhas que representam os novos dados aparecem na tabela de resultados.

Considere um cenário em que está a processar telemetria a partir de sensores de temperatura, como um termóstato. Assuma que o primeiro gatilho processou um evento às 00:01 para o dispositivo 1 com uma leitura de temperatura de 95 graus. No primeiro gatilho da consulta, apenas a linha com o tempo 00:01 aparece na tabela de resultados. Às 00:02 quando chega outro evento, a única nova fila é a fila com o tempo 00:02 e assim a tabela de resultados conteria apenas uma linha.

![Modo de apêndice de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Ao utilizar o modo de apêndice, a sua consulta estaria a aplicar projeções (selecionando as colunas de que se preocupa), filtrando (escolhendo apenas linhas que correspondem a determinadas condições) ou juntando-se (aumentando os dados com dados de uma tabela de procura estática). O modo de apêndice facilita a pressão apenas dos novos dados relevantes para o armazenamento externo.

### <a name="complete-mode"></a>Modo completo

Considere o mesmo cenário, desta vez utilizando o modo completo. Em modo completo, toda a tabela de saídas é atualizada em cada gatilho, pelo que a tabela inclui dados não apenas do mais recente trigger run, mas de todas as execuções. Pode utilizar o modo completo para copiar os dados não utilizados da tabela de entrada para a tabela de resultados. Em cada corrida desencadeada, as novas linhas de resultados aparecem juntamente com todas as linhas anteriores. A tabela de resultados de saída acabará por armazenar todos os dados recolhidos desde o início da consulta, e acabaria por ficar sem memória. O modo completo destina-se a ser utilizado com consultas agregadas que resumem os dados recebidos de alguma forma, pelo que em cada gatilho a tabela de resultados é atualizada com um novo resumo.

Assuma que até agora há cinco segundos de dados já processados, e é hora de processar os dados para o sexto segundo. A tabela de entrada tem eventos para o tempo 00:01 e tempo 00:03. O objetivo desta consulta de exemplo é dar a temperatura média do dispositivo a cada cinco segundos. A implementação desta consulta aplica-se a um agregado que toma todos os valores que se enquadram em cada janela de 5 segundos, media a temperatura, e produz uma linha para a temperatura média ao longo desse intervalo. No final da primeira janela de 5 segundos, há dois tuples: (00:01, 1,95) e (00:03, 1,98). Assim, para a janela 00:00-00:05 a agregação produz uma tuple com a temperatura média de 96,5 graus. Na próxima janela de 5 segundos, só há um ponto de dados no tempo 00:06, então a temperatura média resultante é de 98 graus. No momento 00:10, utilizando o modo completo, a tabela de resultados tem as linhas para ambas as janelas 00:00-00:05 e 00:05-00:10 porque a consulta produz todas as linhas agregadas, e não apenas as novas. Portanto, a tabela de resultados continua a crescer à medida que novas janelas são adicionadas.

![Modo completo de streaming estruturado](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem todas as consultas que utilizam o modo completo farão com que a mesa cresça sem limites.  Considere no exemplo anterior que, em vez de uma média da temperatura por janela de tempo, em vez disso, mediava-se pelo ID do dispositivo. A tabela de resultados contém um número fixo de linhas (uma por dispositivo) com a temperatura média do dispositivo em todos os pontos de dados recebidos desse dispositivo. À medida que as novas temperaturas são recebidas, a tabela de resultados é atualizada para que as médias na tabela estejam sempre atuais.

## <a name="components-of-a-spark-structured-streaming-application"></a>Componentes de uma aplicação de streaming estruturada de faíscas

Uma simples consulta de exemplo pode resumir as leituras de temperatura por janelas de uma hora. Neste caso, os dados são armazenados em ficheiros JSON no Azure Storage (anexado como o armazenamento predefinido para o cluster HDInsight):

```json
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```

Estes ficheiros JSON são armazenados na `temps` subpaga sob o contentor do cluster HDInsight.

### <a name="define-the-input-source"></a>Definir a fonte de entrada

Primeiro configurar um DataFrame que descreve a origem dos dados e quaisquer definições necessárias por essa fonte. Este exemplo baseia-se nos ficheiros JSON no Azure Storage e aplica-lhes um esquema no momento da leitura.

```sql
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

//Cluster-local path to the folder containing the JSON files
val inputPath = "/temps/" 

//Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

//Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
``` 

#### <a name="apply-the-query"></a>Aplicar a consulta

Em seguida, aplique uma consulta que contenha as operações desejadas contra o DataFrame de streaming. Neste caso, uma agregação agrupe todas as linhas em janelas de 1 hora, e depois calcula as temperaturas mínimas, médias e máximas nessa janela de 1 hora.

```sql
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
```

### <a name="define-the-output-sink"></a>Defina o lavatório de saída

Em seguida, defina o destino para as linhas adicionadas à tabela de resultados dentro de cada intervalo de disparo. Este exemplo apenas faz com que todas as linhas para uma tabela de `temps` memórias possa consultar mais tarde com o SparkSQL. O modo de saída completo garante que todas as linhas para todas as janelas são sempre de saída.

```sql
val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete")
``` 

### <a name="start-the-query"></a>Inicie a consulta

Inicie a consulta de streaming e corra até receber um sinal de terminação.

```sql
val query = streamingOutDF.start() 
``` 

### <a name="view-the-results"></a>Ver os resultados

Enquanto a consulta estiver em execução, na mesma SparkSession, pode executar uma consulta SparkSQL contra a `temps` tabela onde os resultados da consulta são armazenados.

```sql
select * from temps
```

Esta consulta produz resultados semelhantes aos seguintes:

| janela |  min(temporário) | avg(temporário) | max(temporário) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Para mais informações sobre a API do Fluxo Estruturado de Faísca, juntamente com as fontes de dados de entrada, operações e sumidouros de saída que suporta, consulte [o Guia de Programação de Streaming Estruturado apache Spark.](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)

## <a name="checkpointing-and-write-ahead-logs"></a>Registos de verificação e de inscrição antecipada

Para proporcionar resiliência e tolerância a falhas, o Streaming Estruturado depende *do checkpoint para* garantir que o processamento de fluxo pode continuar ininterrupto, mesmo com falhas no nó. Em HDInsight, a Spark cria postos de controlo para armazenamento durável, seja o Armazenamento Azure ou o Armazenamento do Lago de Dados. Estes postos de controlo armazenam a informação sobre o progresso sobre a consulta de streaming. Além disso, o Streaming Estruturado utiliza um *registo de escrita antecipada* (WAL). O WAL captura dados ingeridos que foram recebidos mas ainda não processados por uma consulta. Se ocorrer uma falha e o processamento for reiniciado do WAL, quaisquer eventos recebidos da fonte não se perdem.

## <a name="deploying-spark-streaming-applications"></a>Implementação de aplicações de streaming de faíscas

Normalmente constrói uma aplicação Spark Streaming localmente num ficheiro JAR e, em seguida, implanta-a para Spark on HDInsight copiando o ficheiro JAR para o armazenamento predefinido anexado ao seu cluster HDInsight. Pode iniciar a sua aplicação com as APIs [Apache Livy](https://livy.incubator.apache.org/) REST disponíveis no seu cluster utilizando uma operação POST. O corpo do POST inclui um documento JSON que fornece o caminho para o seu JAR, o nome da classe cujo método principal define e executa a aplicação de streaming, e opcionalmente os requisitos de recursos do trabalho (como o número de executores, memória e núcleos), e quaisquer configurações que o seu código de aplicação exija.

![Implementação de uma aplicação de streaming de faíscas](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O estado de todos os pedidos também pode ser verificado com um pedido GET contra um ponto final LIVY. Finalmente, pode terminar um pedido de execução emitindo um pedido de DELETE contra o ponto final LIVY. Para mais detalhes sobre a API LIVY, consulte [trabalhos remotos com Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster Apache Spark em HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação estruturado de streaming de faíscas Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Lançar apache spark empregos remotamente com Apache LIVY](apache-spark-livy-rest-interface.md)
