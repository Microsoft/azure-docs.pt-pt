---
title: Migrar cargas de trabalho apache kafka para Azure HDInsight 4.0
description: Saiba como migrar as cargas de trabalho apache kafka em HDInsight 3.6 para HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437005"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrar cargas de trabalho apache kafka para Azure HDInsight 4.0

O Azure HDInsight 4.0 oferece os mais recentes componentes de código aberto com melhorias significativas no desempenho, conectividade e segurança. Este documento explica como migrar as cargas de trabalho apache kafka em HDInsight 3.6 para HDInsight 4.0. Depois de migrar as suas cargas de trabalho para o HDInsight 4.0, pode utilizar muitas das novas funcionalidades que não estão disponíveis no HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Caminhos de migração kafka

HDInsight 3.6 suporta duas versões de Kafka: 1.0.0 e 1.1.0. HDInsight 4.0 suporta versões 1.1.0 e 2.1.0. Dependendo da versão de Kafka e qual a versão do HDInsight que gostaria de executar, existem múltiplos caminhos de migração suportados. Estes caminhos são explicados abaixo e ilustrados no diagrama seguinte.

* **Execute tanto kafka como HDInsight em versões mais recentes (recomendadas)**: Migrar um HDInsight 3.6 e kafka 1.0.0 ou 1.1.0 aplicação para HDInsight 4.0 com Kafka 2.1.0 (caminhos D e E abaixo).
* **Execute o HDInsight na versão mais recente, mas Kafka apenas numa versão mais recente**: Migrar uma aplicação HDInsight 3.6 e Kafka 1.0.0 para HDInsight 4.0 com Kafka 1.1.0 (caminho B abaixo).
* **Executar o HDInsight na versão mais recente, reter**a versão Kafka : Migrar uma aplicação HDInsight 3.6 e Kafka 1.1.0 para HDInsight 4.0 com Kafka 1.1.0 (caminho C abaixo).
* **Execute kafka numa versão mais recente, mantenha a versão HDInsight**: Migrar uma aplicação Kafka 1.0.0 para 1.1.0 e permanecer no HDInsight 3.6 (caminho A abaixo). Note que esta opção ainda exigirá a implementação de um novo cluster. A atualização da versão Kafka num cluster existente não é suportada. Depois de criar um cluster com a versão que deseja, emigre os seus clientes Kafka para usar o novo cluster.

![Upgrade caminhos para Apache Kafka em 3.6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Versões Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Se migrar de Kafka 1.0.0 para 1.1.0 pode aproveitar as seguintes novidades:

* As melhorias no controlador Kafka aceleram a paragem controlada, para que possa reiniciar os corretores e recuperar de problemas mais rapidamente. 
* Melhorias na [lógica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) que lhe permitem ter mais divisórias (e, portanto, mais tópicos) no cluster. 
* Kafka Connect suporta [cabeçalhos de gravação](https://issues.apache.org/jira/browse/KAFKA-5142) e [expressões regulares](https://issues.apache.org/jira/browse/KAFKA-3073) para tópicos. 

Para obter uma lista completa de atualizações, consulte as notas de [lançamento do Apache Kafka 1.1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Se migrar para Kafka 2.1, pode aproveitar as seguintes características:

* Melhor resiliência do corretor devido a um protocolo de replicação melhorado.
* Nova funcionalidade na API KafkaAdminClient.
* Gestão de quotas configuráveis.
* Suporte para a compressão Zstandard.

Para obter uma lista completa de atualizações, consulte [as notas de lançamento de Apache Kafka 2.0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) e as notas de lançamento de Apache Kafka [2.1.](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)

## <a name="kafka-client-compatibility"></a>Compatibilidade do cliente de Kafka

Novos corretores kafka apoiam clientes mais velhos. [KIP-35 - A versão](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) do protocolo de recuperação introduziu um mecanismo para determinar dinamicamente a funcionalidade de um corretor Kafka e [kip-97: A Política de Compatibilidade](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) do Cliente Kafka Melhorada introduziu uma nova política de compatibilidade e garantias para o cliente Java. Anteriormente, um cliente kafka teve de interagir com um corretor da mesma versão ou uma versão mais recente. Agora, versões mais recentes dos clientes Java e outros clientes `librdkafka` que suportam o KIP-35, tais como podem recorrer a tipos de pedidos mais antigos ou lançar erros apropriados se a funcionalidade não estiver disponível.

![Atualizar a compatibilidade do cliente kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Note que isso não significa que o cliente apoie corretores mais velhos.  Para mais informações, consulte [A Matriz](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)de Compatibilidade .

## <a name="general-migration-process"></a>Processo geral de migração

A seguinte orientação de migração pressupõe um cluster Apache Kafka 1.0.0 ou 1.1.0 implantado no HDInsight 3.6 numa única rede virtual. O corretor existente tem alguns tópicos e está a ser ativamente utilizado por produtores e consumidores.

![Atual kafka presumível ambiente](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Para completar a migração, faça os seguintes passos:

1. **Implemente um novo cluster HDInsight 4.0 e clientes para teste.** Implemente um novo cluster HDInsight 4.0 Kafka. Se várias versões de cluster kafka puderem ser selecionadas, é aconselhável selecionar a versão mais recente. Após a implantação, defina alguns parâmetros conforme necessário e crie um tópico com o mesmo nome que o seu ambiente existente. Além disso, detete tLS e traga a encriptação da sua própria chave (BYOK) conforme necessário. Em seguida, verifique se funciona corretamente com o novo cluster.

    ![Implementar novos clusters HDInsight 4.0](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Mude o cluster para a aplicação do produtor e aguarde até que todos os dados da fila sejam consumidos pelos consumidores atuais.** Quando o novo cluster HDInsight 4.0 Kafka estiver pronto, mude o destino de produtor existente para o novo cluster. Deixe-o como está até que a aplicação de consumo existente tenha consumido todos os dados do cluster existente.

    ![Switch cluster para app de produtor](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Ligue o cluster na aplicação do consumidor.** Depois de confirmar que a aplicação de consumo existente terminou de consumir todos os dados do cluster existente, altere a ligação para o novo cluster.

    ![Alternar cluster na aplicação de consumo](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Retire o aglomerado antigo e as aplicações de teste conforme necessário.** Uma vez que o interruptor esteja completo e funcione corretamente, remova o antigo cluster HDInsight 3.6 Kafka e os produtores e consumidores utilizados no teste conforme necessário.

## <a name="next-steps"></a>Passos seguintes

* [Otimização de desempenho para os clusters do HDInsight no Apache Kafka](apache-kafka-performance-tuning.md)
* [Quickstart: Criar o cluster Apache Kafka no Azure HDInsight usando o portal Azure](apache-kafka-get-started.md)
