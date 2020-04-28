---
title: Apache HBase Master falha em começar no Azure HDInsight
description: Apache HBase Master (HMaster) falha em arranque no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887211"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) falha em arranque no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Cenário: Falha de renomeação atómica

### <a name="issue"></a>Problema

Ficheiros inesperados identificados durante o processo de arranque.

### <a name="cause"></a>Causa

Durante o processo de arranque, o HMaster executa muitas etapas de inicialização, incluindo a mudança de dados da pasta scratch (.tmp) para pasta de dados. O HMaster também olha para a pasta de registos (WAL) para ver se existem servidores da região sem resposta.

O HMaster faz um comando básico da lista nas pastas WAL. Se em algum momento, o HMaster vê um ficheiro inesperado em qualquer uma destas pastas, lança uma exceção e não começa.

### <a name="resolution"></a>Resolução

Verifique a pilha de chamadas e tente determinar qual a pasta que pode estar a causar o problema (por exemplo, pode ser a pasta WAL ou a pasta .tmp). Em seguida, no Cloud Explorer ou utilizando comandos HDFS, tente localizar o ficheiro de problemas. Normalmente, isto `*-renamePending.json` é um ficheiro. (O `*-renamePending.json` ficheiro é um ficheiro de diário que é usado para implementar a operação de renome atómica no condutor wasb. Devido a bugs nesta implementação, estes ficheiros podem ser deixados após falhas no processo, e assim por diante.) Eliminar este ficheiro à força, quer no Cloud Explorer, quer utilizando comandos HDFS.

Às vezes, também pode haver `$$$.$$$` um ficheiro temporário chamado algo como neste local. Tem de usar o `ls` comando HDFS para ver este ficheiro; não é possível ver o ficheiro no Cloud Explorer. Para eliminar este ficheiro, utilize `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`o comando HDFS .

Depois de executar estes comandos, o HMaster deve começar imediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Cenário: Sem endereço de servidor listado

### <a name="issue"></a>Problema

Pode ver uma mensagem que `hbase: meta` indica que a mesa não está online. A `hbck` execução `hbase: meta table replicaId 0 is not found on any region.` pode relatar que nos registos do `No server address listed in hbase: meta for region hbase: backup <region name>`HMaster, pode ver a mensagem: .  

### <a name="cause"></a>Causa

O HMaster não conseguiu inicializar depois de reiniciar a HBase.

### <a name="resolution"></a>Resolução

1. Na concha HBase, introduza os seguintes comandos (alterar valores reais conforme aplicável):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Apague `hbase: namespace` a entrada. Esta entrada pode ser o mesmo erro `hbase: namespace` que está a ser reportado quando a mesa é digitalizada.

1. Reinicie o HMaster ativo da Ambari UI para elevar a HBase em estado de funcionamento.

1. Na concha HBase, para elevar todas as tabelas offline, executar o seguinte comando:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Cenário: java.io.IOException: Timedout

### <a name="issue"></a>Problema

HMaster vezes fora com `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`exceção fatal semelhante a: .

### <a name="cause"></a>Causa

Poderá experimentar este problema se tiver muitas mesas e regiões que não foram lavadas quando reiniciar os seus serviços HMaster. O intervalo é um defeito conhecido com hMaster. As tarefas gerais de arranque de cluster podem demorar muito tempo. O HMaster desliga se a tabela do espaço de nome ainda não estiver atribuída. As longas tarefas de arranque acontecem onde existe uma grande quantidade de dados não corados e um prazo de cinco minutos não é suficiente.

### <a name="resolution"></a>Resolução

1. A partir do Apache Ambari UI, vá para **HBase** > **Configs**. No ficheiro `hbase-site.xml` personalizado, adicione a seguinte definição:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Reiniciar os serviços necessários (HMaster, e possivelmente outros serviços HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Cenário: Reinicia o servidor da região frequente

### <a name="issue"></a>Problema

Os nódos os reiniciam periodicamente. A partir dos registos de servidores da região pode ver entradas semelhantes a:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Longa `regionserver` pausa jvm GC. A pausa `regionserver` fará com que não responda e não seja capaz de enviar batimentos cardíacos ao HMaster dentro do intervalo da sessão de zk 40. O HMaster `regionserver` vai acreditar que `regionserver` está morto e vai abortar o e reiniciar.

### <a name="resolution"></a>Resolução

Mude o tempo de tempo `hbase-site` da `zookeeper.session.timeout` sessão `zoo.cfg` do `maxSessionTimeout` Zookeeper, não só a definição, mas também a definição do Zookeeper, tem de ser alterada.

1. Aceda ao Ambari UI, vá ao **HBase -> Configs -> Definições**, na secção Timeouts, alterar o valor do Timeout sessão do Zookeeper.

1. Aceda ao Ambari UI, vá ao **Zookeeper -> Configs -> Custom,** `zoo.cfg`adicione/altere a seguinte definição. Certifique-se de que o `zookeeper.session.timeout`valor é o mesmo que hbase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Reiniciar os serviços necessários.

---

## <a name="scenario-log-splitting-failure"></a>Cenário: Falha de divisão de log

### <a name="issue"></a>Problema

HMasters não conseguiu chegar a um cluster HBase.

### <a name="cause"></a>Causa

Configurações hDFS e HBase configuradas mal configuradas para uma conta de armazenamento secundária.

### <a name="resolution"></a>Resolução

definir hbase.rootdir: wasb://@.blob.core.windows.net/hbase e reiniciar os serviços em Ambari.

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
