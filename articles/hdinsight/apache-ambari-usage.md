---
title: Utilização de Apache Ambari em Azure HDInsight
description: Discussão de como Apache Ambari é usado em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: fe7d6d4e70bc55a6a91d3c1a1b910db4b5469fe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84197080"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Utilização de Apache Ambari em Azure HDInsight

A HDInsight utiliza o Apache Ambari para implantação e gestão de clusters. Os agentes Ambari funcionam em cada nó (cabeçanode, nó do trabalhador, zookeeper e edgenode, se existir). O servidor Ambari funciona apenas com headnode (hn0 ou hn1). Apenas um caso de servidor Ambari será executado ao mesmo tempo. Isto é controlado pelo controlador de falha HDInsight. Quando um dos headnodes estiver em baixo para reiniciar ou manutenção, o outro headnode ficará ativo e o servidor Ambari no segundo headnode será iniciado.

Toda a configuração do cluster deve ser feita através do [UI Ambari,](./hdinsight-hadoop-manage-ambari.md)qualquer alteração local será substituída quando o nó for reiniciado.

## <a name="failover-controller-services"></a>Serviços de controlo de falhas

O controlador de failover HDInsight é também responsável pela atualização do endereço IP do anfitrião headnode, que aponta para o nó de cabeça ativo atual. Todos os agentes Ambari estão configurados para reportar o seu estado e batimentos cardíacos ao hospedeiro de cabeça. O controlador de failover é um conjunto de serviços em execução em cada nó do cluster, se não estiverem a funcionar, o failover de headnode pode não funcionar corretamente e você vai acabar com HTTP 502 ao tentar aceder ao servidor Ambari.

Para verificar qual cabeçanode está ativo, uma maneira é ssh para um dos nós no cluster, em seguida, executar `ping headnodehost` e comparar o IP com o dos dois headnodes.

Se os serviços de controlador de falha não estiverem a funcionar, a falha do headnode pode não acontecer corretamente, o que pode acabar por não funcionar com o servidor Ambari. Para verificar se os serviços de controlo de falhas estão em execução, execute:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Registos

No headnode ativo, pode verificar os registos do servidor Ambari em:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Em qualquer nó no cluster, você pode verificar os registos do agente Ambari em:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sequências de início de serviço

Esta é a sequência de início de serviço na hora do arranque:

1. O agente Hdinsight inicia os serviços de controlo de falhas.
1. Os serviços de controlador de failover iniciam o agente Ambari em todos os nós e servidor Ambari em headnode ativo.

## <a name="ambari-database"></a>Base de Dados Ambari

O HDInsight cria uma base de dados na Base de Dados SQL sob o capot para servir como base de dados para o servidor Ambari. O [nível de serviço predefinido é S0](../azure-sql/database/elastic-pool-scale.md).

Para qualquer cluster com nó de trabalhador conta maior que 16 ao criar o cluster, S2 é o nível de serviço de base de dados.

## <a name="takeaway-points"></a>Pontos de takeaway

Nunca inicie/pare manualmente os serviços de ambari-servidor ou ambari-agent, a menos que esteja a tentar reiniciar o serviço para resolver um problema. Para forçar um fracasso, pode reiniciar o headnode ativo.

Nunca modifique manualmente quaisquer ficheiros de configuração em nenhum nó de cluster, deixe a Ambari UI fazer o trabalho por si.

## <a name="next-steps"></a>Passos seguintes

* [Manage HDInsight clusters by using the Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
* [Gerir os clusters HDInsight utilizando a API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
