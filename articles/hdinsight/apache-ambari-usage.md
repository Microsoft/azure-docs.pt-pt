---
title: Utilização de Apache Ambari em Azure HDInsight
description: Discussão de como Apache Ambari é usado no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067399"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Utilização de Apache Ambari em Azure HDInsight

O HDInsight utiliza o Apache Ambari para a implementação e gestão do cluster. Os agentes ambari correm em todos os nós (nó de cabeça, nó de trabalhador, zookeeper e edgenode se existir). O servidor Ambari funciona apenas com o headnode (hn0 ou hn1). Apenas uma instância do servidor Ambari será executada de uma só vez. Isto é controlado pelo controlador de falha HDInsight. Quando um dos cabeçados estiver em baixo para reiniciar ou manutenção, o outro nódode ficará ativo e o servidor Ambari no segundo nódoo será iniciado.

Toda a configuração do cluster deve ser feita através do [Ambari UI,](./hdinsight-hadoop-manage-ambari.md)qualquer alteração local será substituída quando o nó for reiniciado.

## <a name="failover-controller-services"></a>Serviços de controlador failover

O controlador de failover HDInsight também é responsável pela atualização do endereço IP do anfitrião do headnode, que aponta para o nó de cabeça ativo atual. Todos os agentes ambari estão configurados para reportar o seu estado e batimento cardíaco ao anfitrião do nó de cabeça. O controlador failover é um conjunto de serviços em execução em todos os nós do cluster, se não estiverem em execução, o headnode failover pode não funcionar corretamente e você vai acabar com HTTP 502 ao tentar aceder ao servidor Ambari.

Para verificar qual o nódoactivo ativo, uma maneira é ssh a um `ping headnodehost` dos nós do cluster, em seguida, executar e comparar o IP com o dos dois nódosos.

Se os serviços de controlador failover não estiverem em execução, a falha do nó de cabeça pode não acontecer corretamente, o que pode acabar por não executar o servidor Ambari. Para verificar se os serviços de controlador de failover estão em execução, execute:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Registos

No nódoado ativo, pode verificar os registos do servidor Ambari em:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Em qualquer nó do cluster, pode verificar os registos do agente Ambari em:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sequências de início de serviço

Esta é a sequência de início de serviço no momento do arranque:

1. O agente Hdinsight inicia os serviços de controlador de failover.
1. Os serviços de controlador failover iniciam o agente Ambari em cada nó e servidor Ambari em cabeceira ativa.

## <a name="ambari-database"></a>Base de Dados Ambari

O HDInsight cria a Base de Dados SQL Azure sob o capot para servir de base de dados para o servidor Ambari. O [nível de serviço](../sql-database/sql-database-elastic-pool-scale.md)predefinido é S0 .

Para qualquer cluster com nó de trabalhador esmorecê-lo com maior número de 16 quando criar o cluster, o S2 é o nível de serviço de base de dados.

## <a name="takeaway-points"></a>Pontos de takeaway

Nunca inicie/pare manualmente os serviços de ambari-server ou ambari-agent, a menos que esteja a tentar reiniciar o serviço para resolver um problema. Para forçar uma falha, pode reiniciar o nódoaactivo.

Nunca modifique manualmente quaisquer ficheiros de configuração em qualquer nó de cluster, deixe a Ambari UI fazer o trabalho por si.

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters do HDInsight através da IU da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Gerir os clusters HDInsight utilizando a API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
