---
title: Processamento de eventos de streaming do Spark & exatamente uma vez – Azure HDInsight
description: Como configurar Apache Spark streaming para processar um evento apenas uma vez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228977"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Criar Apache Spark trabalhos de streaming com processamento de eventos exatamente uma vez

Os aplicativos de processamento de fluxo assumem abordagens diferentes sobre como eles manipulam o reprocessamento de mensagens após alguma falha no sistema:

* Pelo menos uma vez: cada mensagem é garantida para ser processada, mas pode ser processada mais de uma vez.
* No máximo uma vez: cada mensagem pode ou não ser processada. Se uma mensagem for processada, ela será processada apenas uma vez.
* Exatamente uma vez: cada mensagem é garantida para ser processada apenas uma vez.

Este artigo mostra como configurar o streaming do Spark para obter um processamento exatamente uma vez.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Semânticas exatamente uma vez com Apache Spark streaming

Primeiro, considere como todos os pontos do sistema de falha são reiniciados depois de ter um problema e como você pode evitar a perda de dados. Um aplicativo Spark streaming tem:

* Uma fonte de entrada.
* Um ou mais processos receptores que efetuam pull de dados da fonte de entrada.
* Tarefas que processam os dados.
* Um coletor de saída.
* Um processo de driver que gerencia o trabalho de execução longa.

Semânticas exatamente uma vez exigem que nenhum dado seja perdido em qualquer ponto e que o processamento de mensagens seja reinicializável, independentemente de onde a falha ocorra.

### <a name="replayable-sources"></a>Fontes reproduzidas

A fonte da qual seu aplicativo de streaming do Spark está lendo os eventos deve ser *reproduzida*. Isso significa que, nos casos em que a mensagem foi recuperada, mas o sistema falhou antes que a mensagem possa ser persistida ou processada, a origem deve fornecer a mesma mensagem novamente.

No Azure, os hubs de eventos do Azure e o [Apache Kafka](https://kafka.apache.org/) no HDInsight fornecem fontes que permitem reprodução. Outro exemplo de uma fonte que pode ser reproduzida é um sistema de arquivos tolerante a falhas como [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), blobs de armazenamento do Azure ou Azure data Lake Storage, em que todos os dados são mantidos para sempre e a qualquer momento, você pode ler novamente os dados em sua totalidade.

### <a name="reliable-receivers"></a>Receptores confiáveis

No streaming do Spark, fontes como hubs de eventos e Kafka têm *receptores confiáveis*, onde cada receptor controla seu progresso na leitura da origem. Um receptor confiável persiste seu estado no armazenamento tolerante a falhas, seja dentro [Apache ZooKeeper](https://zookeeper.apache.org/) ou em pontos de verificação de streaming do Spark gravados no HDFS. Se esse receptor falhar e for reiniciado posteriormente, ele poderá continuar de onde parou.

### <a name="use-the-write-ahead-log"></a>Usar o log write-ahead

O streaming do Spark dá suporte ao uso de um log write-ahead, onde cada evento recebido é gravado primeiro no diretório de ponto de verificação do Spark no armazenamento tolerante a falhas e, em seguida, armazenado em um RDD (conjunto de registros distribuído resiliente). No Azure, o armazenamento tolerante a falhas é o HDFS apoiado pelo armazenamento do Azure ou Azure Data Lake Storage. No aplicativo Spark streaming, o log write-ahead é habilitado para todos os receptores, definindo o parâmetro de configuração `spark.streaming.receiver.writeAheadLog.enable` como `true`. O log write-ahead fornece tolerância a falhas para falhas do driver e dos executores.

Para os trabalhos que executam tarefas em relação aos dados do evento, cada RDD é por definição replicada e distribuída entre vários trabalhadores. Se uma tarefa falhar porque o trabalho que a executa falhou, a tarefa será reiniciada em outro trabalho que tenha uma réplica dos dados do evento, de modo que o evento não seja perdido.

### <a name="use-checkpoints-for-drivers"></a>Usar pontos de verificação para drivers

Os drivers de trabalho precisam ser reinicializáveis. Se o driver que executa o aplicativo Spark streaming falhar, ele desaparecerá com ele todos os receptores em execução, tarefas e qualquer RDDs de armazenamento de dados de evento. Nesse caso, você precisa ser capaz de salvar o progresso do trabalho para que possa retomá-lo mais tarde. Isso é feito por meio do ponto de verificação do DAG (grafo direcionado acíclico) do DStream periodicamente para o armazenamento tolerante a falhas. Os metadados do DAG incluem a configuração usada para criar o aplicativo de streaming, as operações que definem o aplicativo e os lotes que estão na fila, mas ainda não foram concluídos. Esses metadados permitem que um driver com falha seja reiniciado a partir das informações de ponto de verificação. Quando o driver for reiniciado, ele iniciará novos receptores que recuperam os dados de evento de volta no RDDs do log write-ahead.

Os pontos de verificação são habilitados no streaming do Spark em duas etapas.

1. No objeto StreamingContext, configure o caminho de armazenamento para os pontos de verificação:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    No HDInsight, esses pontos de verificação devem ser salvos no armazenamento padrão anexado ao cluster, tanto no armazenamento do Azure quanto no Azure Data Lake Storage.

2. Em seguida, especifique um intervalo de ponto de verificação (em segundos) no DStream. Em cada intervalo, os dados de estado derivados do evento de entrada são persistidos no armazenamento. Os dados de estado persistentes podem reduzir a computação necessária ao recriar o estado a partir do evento de origem.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Usar coletores idempotentes

O coletor de destino para o qual seu trabalho grava os resultados deve ser capaz de lidar com a situação em que ele recebe o mesmo resultado mais de uma vez. O coletor deve ser capaz de detectar esses resultados duplicados e ignorá-los. Um coletor *idempotente* pode ser chamado várias vezes com os mesmos dados sem nenhuma alteração de estado.

Você pode criar coletores idempotentes implementando a lógica que primeiro verifica a existência do resultado de entrada no repositório de armazenamento. Se o resultado já existir, a gravação deverá parecer ter sucesso da perspectiva do seu trabalho do Spark, mas, na realidade, o armazenamento de dados ignorou os dados duplicados. Se o resultado não existir, o coletor deverá inserir esse novo resultado em seu armazenamento.

Por exemplo, você pode usar um procedimento armazenado com o banco de dados SQL do Azure que insere eventos em uma tabela. Esse procedimento armazenado primeiro pesquisa o evento por campos de chave e somente quando nenhum evento correspondente encontrado é o registro inserido na tabela.

Outro exemplo é usar um sistema de arquivos particionado, como BLOBs de armazenamento do Azure ou Azure Data Lake Storage. Nesse caso, a lógica do coletor não precisa verificar a existência de um arquivo. Se o arquivo que representa o evento existir, ele será simplesmente substituído pelos mesmos dados. Caso contrário, um novo arquivo será criado no caminho computado.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral de streaming de Apache Spark](apache-spark-streaming-overview.md)
* [Criando trabalhos de streaming de Apache Spark altamente disponíveis no Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
