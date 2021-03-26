---
title: Migrar cargas de carga Apache Kafka para Azure HDInsight 4.0
description: Saiba como migrar cargas de trabalho apache Kafka em HDInsight 3.6 a HDInsight 4.0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: e15ebb13aee0e5dd814688ae77edaded667d54ac
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864130"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrar cargas de carga Apache Kafka para Azure HDInsight 4.0

O Azure HDInsight 4.0 oferece os mais recentes componentes de código aberto com melhorias significativas no desempenho, conectividade e segurança. Este documento explica como migrar cargas de trabalho apache Kafka em HDInsight 3.6 a HDInsight 4.0. Depois de migrar as suas cargas de trabalho para HDInsight 4.0, pode utilizar muitas das novas funcionalidades que não estão disponíveis no HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Caminhos de migração kafka

HDInsight 3.6 suporta duas versões de Kafka: 1.0.0 e 1.1.0. HDInsight 4.0 suporta as versões 1.1.0 e 2.1.0. Dependendo da versão de Kafka e qual a versão do HDInsight que gostaria de executar, existem vários caminhos de migração suportados. Estes caminhos são explicados abaixo e ilustrados no diagrama seguinte.

* **Executar tanto Kafka como HDInsight nas versões mais recentes (recomendado)**: Migrar uma hdInsight 3.6 e Kafka 1.0.0 ou 1.1.0 para HDInsight 4.0 com Kafka 2.1.0 (caminhos D e E abaixo).
* **Executar HDInsight na versão mais recente, mas Kafka apenas numa versão mais recente**: Migrar uma aplicação HDInsight 3.6 e Kafka 1.0.0 para HDInsight 4.0 com Kafka 1.1.0 (caminho B abaixo).
* **Executar HDInsight na versão mais recente, reter a versão Kafka**: Migrar uma aplicação HDInsight 3.6 e Kafka 1.1.0 para HDInsight 4.0 com Kafka 1.1.0 (caminho C abaixo).
* **Executar Kafka numa versão mais recente, reter a versão HDInsight**: Migrar uma aplicação Kafka 1.0.0 para 1.1.0 e permanecer no HDInsight 3.6 (caminho A abaixo). Note que esta opção ainda requer a implantação de um novo cluster. A atualização da versão Kafka num cluster existente não é suportada. Depois de criar um cluster com a versão que pretende, migrar os seus clientes Kafka para utilizar o novo cluster.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png" alt-text="Upgrade de caminhos para Apache Kafka em 3.6" border="false":::

## <a name="apache-kafka-versions"></a>Versões Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Se migrar de Kafka 1.0.0 para 1.1.0 pode aproveitar as seguintes novas funcionalidades:

* Melhorias no controlador Kafka aceleram o encerramento controlado, para que possa reiniciar os corretores e recuperar de problemas mais rapidamente. 
* Melhorias na [lógica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) que lhe permitem ter mais divisórias (e, portanto, mais tópicos) no cluster. 
* O Kafka Connect suporta [cabeçalhos de gravação](https://issues.apache.org/jira/browse/KAFKA-5142) e [expressões regulares](https://issues.apache.org/jira/browse/KAFKA-3073) para tópicos. 

Para obter uma lista completa de atualizações, consulte as notas de [lançamento apache Kafka 1.1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Se migrar para Kafka 2.1, pode aproveitar as seguintes características:

* Melhor resiliência do corretor devido a um protocolo de replicação melhorado.
* Nova funcionalidade na API KafkaAdminClient.
* Gestão de quotas configuráveis.
* Suporte para compressão Zstandard.

Para obter uma lista completa de atualizações, consulte as notas de [lançamento apache Kafka 2.0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) e [as notas de lançamento apache Kafka 2.1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Compatibilidade do cliente kafka

Os novos corretores Kafka apoiam clientes mais velhos. [KIP-35 - A versão do protocolo de recuperação](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) introduziu um mecanismo para determinar dinamicamente a funcionalidade de um corretor Kafka e [KIP-97: A Política de Compatibilidade do Cliente de Kafka RPC introduziu](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) uma nova política de compatibilidade e garantias para o cliente Java. Anteriormente, um cliente kafka teve de interagir com um corretor da mesma versão ou uma versão mais recente. Agora, versões mais recentes dos clientes Java e outros clientes que suportam o KIP-35, como `librdkafka` podem recair em tipos de pedidos mais antigos ou lançar erros apropriados se a funcionalidade não estiver disponível.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png" alt-text="Atualizar compatibilidade do cliente kafka" border="false":::

Note que não significa que o cliente suporte corretores mais velhos.  Para mais informações, consulte [a Matriz de Compatibilidade.](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)

## <a name="general-migration-process"></a>Processo geral de migração

A seguinte orientação de migração pressupõe um aglomerado Apache Kafka 1.0.0 ou 1.1.0 implantado no HDInsight 3.6 numa única rede virtual. O corretor existente tem alguns tópicos e está a ser ativamente utilizado por produtores e consumidores.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png" alt-text="Ambiente presumido de Kafka atual" border="false":::

Para completar a migração, faça os seguintes passos:

1. **Implementar um novo cluster HDInsight 4.0 e clientes para teste.** Implementar um novo cluster HDInsight 4.0 Kafka. Se várias versões de cluster Kafka puderem ser selecionadas, é aconselhável selecionar a versão mais recente. Após a implementação, desaprote alguns parâmetros conforme necessário e crie um tópico com o mesmo nome que o ambiente existente. Além disso, desa parte para a encriptação TLS e bring-your-your-key (BYOK) conforme necessário. Em seguida, verifique se funciona corretamente com o novo cluster.

    :::image type="content" source="./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png" alt-text="Implementar novos clusters HDInsight 4.0" border="false":::

1. **Troque o cluster para a aplicação do produtor e aguarde até que todos os dados da fila são consumidos pelos consumidores atuais.** Quando o novo cluster HDInsight 4.0 Kafka estiver pronto, mude o destino de produtor existente para o novo cluster. Deixe-o como está até que a app consumer existente tenha consumido todos os dados do cluster existente.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-producer-app.png" alt-text="Mude o cluster para app de produtor" border="false":::

1. **Mude o cluster na aplicação do consumidor.** Depois de confirmar que a aplicação de consumo existente terminou de consumir todos os dados do cluster existente, altere a ligação para o novo cluster.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png" alt-text="Mude o cluster na app do consumidor" border="false":::

1. **Retire o aglomerado antigo e as aplicações de teste conforme necessário.** Uma vez que o interruptor esteja completo e funcionando corretamente, remova o antigo cluster HDInsight 3.6 Kafka e os produtores e consumidores utilizados no teste, conforme necessário.

## <a name="next-steps"></a>Passos seguintes

* [Otimização de desempenho para os clusters do HDInsight no Apache Kafka](apache-kafka-performance-tuning.md)
* [Quickstart: Criar cluster Apache Kafka em Azure HDInsight usando o portal Azure](apache-kafka-get-started.md)
