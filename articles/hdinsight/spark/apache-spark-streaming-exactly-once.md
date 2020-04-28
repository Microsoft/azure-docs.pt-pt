---
title: Spark Streaming & processamento de eventos exatamente uma vez - Azure HDInsight
description: Como configurar o Apache Spark Streaming para processar um evento uma vez e apenas uma vez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74228977"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Crie empregos de Streaming de Faíscas Apache com processamento de eventos exatamente uma vez

As aplicações de processamento de fluxo saem diferentes abordagens de como lidam com mensagens de reprocessamento após alguma falha no sistema:

* Pelo menos uma vez: Cada mensagem é garantida para ser processada, mas pode ser processada mais de uma vez.
* No máximo, uma vez: Cada mensagem pode ou não ser processada. Se uma mensagem for processada, só é processada uma vez.
* Exatamente uma vez: Cada mensagem é garantida para ser processada uma vez e apenas uma vez.

Este artigo mostra-lhe como configurar o Spark Streaming para conseguir exatamente um processamento.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Exatamente uma vez semântica com Apache Spark Streaming

Primeiro, considere como todos os pontos de falha do sistema recomeçam depois de ter um problema, e como pode evitar a perda de dados. Uma aplicação Spark Streaming tem:

* Uma fonte de entrada.
* Um ou mais processos recetores que retiram dados da fonte de entrada.
* Tarefas que processam os dados.
* Uma pia de saída.
* Um processo de motorista que gere o trabalho de longa duração.

Exatamente uma vez que a semântica exija que nenhum dado seja perdido em nenhum momento, e que o processamento de mensagens é reiniciado, independentemente do local onde a falha ocorra.

### <a name="replayable-sources"></a>Fontes retáveis

A fonte da sua aplicação Spark Streaming está a ler os seus eventos deve ser *reempável*. Isto significa que nos casos em que a mensagem foi recuperada, mas depois o sistema falhou antes de a mensagem poder ser persistiu ou processada, a fonte deve voltar a fornecer a mesma mensagem.

Em Azure, tanto os Hubs de Eventos Azure como [o Apache Kafka](https://kafka.apache.org/) no HDInsight fornecem fontes reutilizáveis. Outro exemplo de uma fonte reutilizável é um sistema de ficheiros tolerante a falhas como [o Apache Hadoop HDFS,](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)blobs de armazenamento azure ou armazenamento de lagos de dados Azure, onde todos os dados são mantidos para sempre e em qualquer momento você pode reler os dados na sua totalidade.

### <a name="reliable-receivers"></a>Recetores fiáveis

No Spark Streaming, fontes como Os Hubs de Eventos e Kafka têm *recetores fiáveis,* onde cada recetor acompanha o seu progresso a ler a fonte. Um recetor fiável persiste o seu estado em armazenamento tolerante a falhas, seja dentro do [Apache ZooKeeper](https://zookeeper.apache.org/) ou nos postos de controlo de Spark Streaming escritos ao HDFS. Se tal recetor falhar e for posteriormente reiniciado, pode retomar o local onde ficou parado.

### <a name="use-the-write-ahead-log"></a>Use o registo write-ahead

O Spark Streaming suporta a utilização de um Registo Write-Ahead, onde cada evento recebido é escrito pela primeira vez para o diretório de checkpoint da Spark em armazenamento tolerante a falhas e depois armazenado num Conjunto de Dados Distribuídos Resiliente (RDD). Em Azure, o armazenamento tolerante a falhas é o HDFS apoiado pelo Armazenamento Azure ou pelo Armazenamento do Lago Azure Data. Na sua aplicação Spark Streaming, o Registo Write-Ahead está `spark.streaming.receiver.writeAheadLog.enable` ativado `true`para todos os recetores, definindo a definição de configuração para . O Registo Write-Ahead proporciona tolerância a falhas tanto do condutor como dos executores.

Para os trabalhadores que executam tarefas contra os dados do evento, cada RDD é, por definição, replicado e distribuído por vários trabalhadores. Se uma tarefa falhar porque o trabalhador que a executou se despenhou, a tarefa será reiniciada noutro trabalhador que tenha uma réplica dos dados do evento, pelo que o evento não se perde.

### <a name="use-checkpoints-for-drivers"></a>Utilize postos de controlo para condutores

Os motoristas de trabalho precisam de ser reiniciados. Se o condutor que executa a sua aplicação Spark Streaming falhar, retira-se com todos os recetores de execução, tarefas e quaisquer RDDs que armazenem dados de eventos. Neste caso, você precisa ser capaz de salvar o progresso do trabalho para que possa retomá-lo mais tarde. Isto é conseguido através do checkpoint do Gráfico Acíclico Direcionado (DAG) do DStream periodicamente para armazenamento tolerante a falhas. Os metadados DAG incluem a configuração utilizada para criar a aplicação de streaming, as operações que definem a aplicação, e quaisquer lotes que estejam na fila mas ainda não concluídos. Estes metadados permitem que um controlador falhado seja reiniciado a partir da informação do checkpoint. Quando o controlador reiniciar, lançará novos recetores que, por si só, recuperem os dados do evento em RDDs do Registo Write-Ahead.

Os pontos de verificação estão ativados no Spark Streaming em dois passos.

1. No objeto StreamingContext, configure o caminho de armazenamento para os pontos de verificação:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    No HDInsight, estes pontos de verificação devem ser guardados no armazenamento predefinido ligado ao seu cluster, quer no Armazenamento Azure quer no Armazenamento de Lagos De dados Azure.

2. Em seguida, especifique um intervalo de controlo (em segundos) no DStream. Em cada intervalo, os dados estatais derivados do evento de entrada são persistidos para armazenamento. Os dados estatais persistidos podem reduzir o cálculo necessário na reconstrução do Estado a partir do evento de origem.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Use pias idempotentes

O afundado de destino ao qual o seu trabalho escreve resultados deve ser capaz de lidar com a situação em que tem o mesmo resultado mais de uma vez. O lavatório deve ser capaz de detetar tais resultados duplicados e ignorá-los. Um lavatório *idempotente* pode ser chamado várias vezes com os mesmos dados sem alteração de estado.

Pode criar pias idempotentes implementando a lógica que verifica primeiro a existência do resultado na loja de dados. Se o resultado já existir, a escrita deverá parecer ter sucesso na perspetiva do seu trabalho spark, mas na realidade a sua loja de dados ignorou os dados duplicados. Se o resultado não existir, então a pia deve inserir este novo resultado no seu armazenamento.

Por exemplo, pode utilizar um procedimento armazenado com base de dados Azure SQL que insere eventos numa tabela. Este procedimento armazenado primeiro olha para o evento por campos-chave, e só quando nenhum evento correspondente encontrado é o registo inserido na tabela.

Outro exemplo é usar um sistema de ficheiros dividido, como as bolhas de armazenamento de Azure ou o Armazenamento do Lago De Dados Azure. Neste caso, a sua lógica de afundar não precisa de verificar a existência de um ficheiro. Se o ficheiro que representa o evento existir, é simplesmente substituído com os mesmos dados. Caso contrário, um novo ficheiro é criado no caminho computacional.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do streaming de faíscas Apache](apache-spark-streaming-overview.md)
* [Criação de empregos de streaming de faíscas Apache altamente disponíveis no YARN Apache Hadoop](apache-spark-streaming-high-availability.md)
