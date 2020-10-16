---
title: Apache HBase Master falha arranque no Azure HDInsight
description: Apache HBase Master (HMaster) falha no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887211"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) falha no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Cenário: Falha de renomeação atómica

### <a name="issue"></a>Problema

Ficheiros inesperados identificados durante o processo de arranque.

### <a name="cause"></a>Causa

Durante o processo de arranque, o HMaster executa muitas etapas de inicialização, incluindo a transferência de dados do risco (.tmp) para a pasta de dados. O HMaster também olha para a pasta de registos de escrita (WAL) para ver se existem servidores da região sem resposta.

HMaster faz um comando de lista básica nas pastas WAL. Se em qualquer momento, o HMaster vir um ficheiro inesperado em qualquer uma destas pastas, lança uma exceção e não arranca.

### <a name="resolution"></a>Resolução

Verifique a pilha de chamadas e tente determinar qual a pasta que pode estar a causar o problema (por exemplo, pode ser a pasta WAL ou a pasta .tmp). Em seguida, no Cloud Explorer ou utilizando comandos HDFS, tente localizar o ficheiro problemático. Normalmente, isto é um `*-renamePending.json` ficheiro. (O `*-renamePending.json` ficheiro é um ficheiro de diário que é usado para implementar a operação de renome atómico no controlador WASB. Devido a bugs nesta implementação, estes ficheiros podem ser deixados após falhas de processo, e assim por diante.) Elimine este ficheiro, quer no Cloud Explorer, quer através de comandos HDFS.

Às vezes, também pode haver um ficheiro temporário chamado algo como `$$$.$$$` neste local. Tem de utilizar o comando HDFS `ls` para ver este ficheiro; não é possível ver o ficheiro no Cloud Explorer. Para eliminar este ficheiro, utilize o comando HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` .

Depois de executar estes comandos, o HMaster deve começar imediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Cenário: Sem endereço de servidor listado

### <a name="issue"></a>Problema

Pode ver uma mensagem que indica que a `hbase: meta` mesa não está online. A `hbck` execução pode informar que `hbase: meta table replicaId 0 is not found on any region.` nos registos HMaster, pode ver a mensagem: `No server address listed in hbase: meta for region hbase: backup <region name>` .  

### <a name="cause"></a>Causa

HMaster não pôde inicializar depois de reiniciar a Base H.

### <a name="resolution"></a>Resolução

1. No reservatório HBase, introduza os seguintes comandos (alterar valores reais conforme aplicável):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Apague a `hbase: namespace` entrada. Esta entrada pode ser o mesmo erro que está a ser reportado quando a `hbase: namespace` tabela é digitalizada.

1. Reinicie o HMaster ativo da Ambari UI para trazer hbase em estado de execução.

1. Na concha HBase, para trazer todas as tabelas offline, executar o seguinte comando:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Cenário: java.io.IOExcepção: Timedout

### <a name="issue"></a>Problema

HMaster times out com exceção fatal semelhante a: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` .

### <a name="cause"></a>Causa

Poderá experimentar este problema se tiver muitas tabelas e regiões que não foram lavadas quando reiniciar os seus serviços HMaster. O tempo de 2019 é um defeito conhecido com o HMaster. As tarefas gerais de arranque do cluster podem demorar muito tempo. HMaster desliga se a tabela do espaço de nomes ainda não estiver atribuída. As longas tarefas de arranque acontecem onde existe uma grande quantidade de dados não flushed e um intervalo de cinco minutos não é suficiente.

### <a name="resolution"></a>Resolução

1. Da Apache Ambari UI, vá à **HBase**  >  **Configs.** No `hbase-site.xml` ficheiro personalizado, adicione a seguinte definição:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Reinicie os serviços necessários (HMaster e, possivelmente, outros serviços HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Cenário: Reinício frequente do servidor da região

### <a name="issue"></a>Problema

Os nós reiniciam periodicamente. A partir dos registos de servidores da região, poderá ver entradas semelhantes a:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Longa `regionserver` pausa JVM GC. A pausa fará com que `regionserver` não responda e não seja capaz de enviar batimentos cardíacos para o HMaster dentro do tempo limite de 40 sessão do ZK. HMaster vai acreditar que `regionserver` está morto e vai abortar e `regionserver` reiniciar.

### <a name="resolution"></a>Resolução

Mude o tempo limite de sessão do Zookeeper, não só `hbase-site` a `zookeeper.session.timeout` definição, mas também a definição do Zookeeper. `zoo.cfg` `maxSessionTimeout`

1. Aceda a Ambari UI, vá a **HBase -> Configs -> Definições**, na secção Timeouts, altere o valor do Tempo de Sessão zookeeper.

1. Aceda a Ambari UI, vá ao **Zookeeper -> Configs -> Custom** `zoo.cfg` , adicione/altere a seguinte definição. Certifique-se de que o valor é o mesmo que a HBase `zookeeper.session.timeout` .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Reiniciar os serviços necessários.

---

## <a name="scenario-log-splitting-failure"></a>Cenário: Falha na divisão de registos

### <a name="issue"></a>Problema

A HMasters não conseguiu chegar a um cluster HBase.

### <a name="cause"></a>Causa

Configurações HDFS e HBase mal configuradas para uma conta de armazenamento secundária.

### <a name="resolution"></a>Resolução

definir hbase.rootdir: wasb://@.blob.core.windows.net/hbase e reiniciar os serviços em Ambari.

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
