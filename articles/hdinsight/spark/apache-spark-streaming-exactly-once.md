---
title: Spark Streaming & processamento de eventos exatamente uma vez - Azure HDInsight
description: Como configurar o Apache Spark Streaming para processar um evento uma vez e só uma vez.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2018
ms.openlocfilehash: 4ba7df665b24a3eba2cd185d85a17bd0ef456b0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929662"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Criar empregos em streaming de Faíscas Apache Com processamento de eventos exatamente uma vez

As aplicações de processamento de fluxos têm diferentes abordagens sobre como lidam com mensagens de reprocessamento após alguma falha no sistema:

* Pelo menos uma vez: Cada mensagem é garantida para ser processada, mas pode ser processada mais de uma vez.
* No máximo: Cada mensagem pode ou não ser processada. Se uma mensagem é processada, só é processada uma vez.
* Exatamente uma vez: Cada mensagem é garantida para ser processada uma vez e apenas uma vez.

Este artigo mostra-lhe como configurar o Spark Streaming para obter exatamente uma vez processamento.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Semântica exatamente uma vez com Apache Spark Streaming

Em primeiro lugar, considere como todos os pontos de falha do sistema reiniciam depois de ter um problema e como pode evitar a perda de dados. Uma aplicação spark streaming tem:

* Uma fonte de entrada.
* Um ou mais processos de recetor que retiram dados da fonte de entrada.
* Tarefas que processam os dados.
* Um lavatório de saída.
* Um processo de motorista que gere o trabalho de longa duração.

A semântica exatamente uma vez requer que nenhum dado seja perdido em nenhum ponto, e que o processamento de mensagens seja reiniciável, independentemente do local onde a falha ocorra.

### <a name="replayable-sources"></a>Fontes reprodução

A fonte da sua aplicação Spark Streaming está a ler os seus eventos deve ser *reproduzível*. Isto significa que nos casos em que a mensagem foi recuperada, mas depois o sistema falhou antes de a mensagem poder ser persistido ou processada, a fonte deve fornecer novamente a mesma mensagem.

Em Azure, tanto os Azure Event Hubs como [o Apache Kafka](https://kafka.apache.org/) na HDInsight fornecem fontes reproduzíveis. Outro exemplo de uma fonte reprodução é um sistema de ficheiros tolerantes a falhas como [o Apache Hadoop HDFS,](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)as bolhas de armazenamento Azure Storage ou o Azure Data Lake Storage, onde todos os dados são mantidos para sempre e em qualquer ponto pode reler os dados na sua totalidade.

### <a name="reliable-receivers"></a>Recetores fiáveis

No Spark Streaming, fontes como os Event Hubs e Kafka têm *recetores fiáveis,* onde cada recetor acompanha o seu progresso lendo a fonte. Um recetor fiável persiste o seu estado em armazenamento tolerante a falhas, quer dentro do [Apache ZooKeeper,](https://zookeeper.apache.org/) quer nos pontos de verificação de streaming spark escritos para HDFS. Se um recetor falhar e for reiniciado mais tarde, pode recomeçar onde para fora.

### <a name="use-the-write-ahead-log"></a>Use o registo de Write-Ahead

O Spark Streaming suporta a utilização de um Registo de Write-Ahead, onde cada evento recebido é escrito pela primeira vez para o diretório de checkpoint da Spark no armazenamento tolerante a falhas e depois armazenado num Conjunto de Dados Distribuídos Resiliente (RDD). Em Azure, o armazenamento tolerante a falhas é apoiado por Azure Storage ou Azure Data Lake Storage. Na sua aplicação Spark Streaming, o Registo de Write-Ahead está ativado para todos os recetores definindo a definição de `spark.streaming.receiver.writeAheadLog.enable` configuração para `true` . O Write-Ahead Log proporciona tolerância a falhas tanto do condutor como dos executores.

Para os trabalhadores que executam tarefas contra os dados do evento, cada RDD é, por definição, replicado e distribuído por vários trabalhadores. Se uma tarefa falhar porque o trabalhador que o executa se despenhou, a tarefa será reiniciada noutro trabalhador que tenha uma réplica dos dados do evento, pelo que o evento não se perde.

### <a name="use-checkpoints-for-drivers"></a>Utilize postos de controlo para condutores

Os motoristas de emprego têm de ser reiniciáveis. Se o condutor que executa a sua aplicação Spark Streaming falhar, ele retira-se com todos os recetores, tarefas e quaisquer RDDs que armazenam dados do evento. Neste caso, precisa de ser capaz de salvar o progresso do trabalho para que possa retomá-lo mais tarde. Isto é conseguido através do checkpoint do Gráfico Aciclico Direcionado (DAG) do DStream periodicamente para o armazenamento tolerante a falhas. Os metadados DAG incluem a configuração utilizada para criar a aplicação de streaming, as operações que definem a aplicação, e quaisquer lotes que estejam na fila mas ainda não concluídos. Estes metadados permitem reiniciar o controlador a partir da informação do ponto de verificação. Quando o condutor recomeçar, lançará novos recetores que, por si só, recuperarão os dados do evento de volta aos RDDs a partir do Registo de Write-Ahead.

Os postos de controlo estão ativados em Spark Streaming em dois passos.

1. No objeto StreamingContext, configurar a trajetória de armazenamento para os pontos de verificação:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    No HDInsight, estes pontos de verificação devem ser guardados para o armazenamento predefinido ligado ao seu cluster, seja o Azure Storage ou o Azure Data Lake Storage.

2. Em seguida, especifique um intervalo de verificação (em segundos) no DStream. Em cada intervalo, os dados estatais derivados do evento de entrada são percamados para o armazenamento. Os dados estatais persistentes podem reduzir o cálculo necessário para a reconstrução do estado a partir do evento de origem.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Use pias idempotentes

O destino para o qual o seu trabalho escreve resultados deve ser capaz de lidar com a situação em que é dado o mesmo resultado mais de uma vez. A pia deve ser capaz de detetar tais resultados duplicados e ignorá-los. Uma pia *idempotente* pode ser chamada várias vezes com os mesmos dados sem alteração de estado.

Pode criar pias idempotentes implementando lógica que verifica primeiro a existência do resultado de entrada na datastore. Se o resultado já existir, a escrita deverá parecer ter sucesso na perspetiva do seu trabalho de Spark, mas na realidade a sua loja de dados ignorou os dados duplicados. Se o resultado não existir, então a pia deve inserir este novo resultado no seu armazenamento.

Por exemplo, pode utilizar um procedimento armazenado com base de dados Azure SQL que insere eventos numa tabela. Este procedimento armazenado procura primeiro o evento por campos-chave, e só quando nenhum evento correspondente encontrado é o registo inserido na tabela.

Outro exemplo é usar um sistema de ficheiros dividido, como bolhas de armazenamento Azure ou armazenamento de data de Azure. Neste caso, a sua lógica de sumidouro não precisa de verificar a existência de um ficheiro. Se o ficheiro que representa o evento existir, é simplesmente substituído com os mesmos dados. Caso contrário, um novo ficheiro é criado no caminho calculado.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do fluxo de faíscas de Apache Spark](apache-spark-streaming-overview.md)
* [Criação de empregos altamente disponíveis em Apache Spark Streaming em Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
