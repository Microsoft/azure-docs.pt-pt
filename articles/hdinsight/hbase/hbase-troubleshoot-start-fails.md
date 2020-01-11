---
title: O Apache HBase Master não é iniciado no Azure HDInsight
description: O Apache HBase Master (HMaster) não é iniciado no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887211"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>O Apache HBase Master (HMaster) não é iniciado no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Cenário: falha de renomeação atômica

### <a name="issue"></a>Problema

Arquivos inesperados identificados durante o processo de inicialização.

### <a name="cause"></a>Causa

Durante o processo de inicialização, o HMaster executa muitas etapas de inicialização, incluindo a movimentação de dados da pasta Scratch (. tmp) para a pasta de dados. O HMaster também examina a pasta logs write-ahead (WAL) para ver se há servidores de região sem resposta.

HMaster faz um comando de lista básica nas pastas WAL. Se, a qualquer momento, o HMaster vir um arquivo inesperado em qualquer uma dessas pastas, ele lançará uma exceção e não será iniciado.

### <a name="resolution"></a>Resolução

Verifique a pilha de chamadas e tente determinar qual pasta pode estar causando o problema (por exemplo, pode ser a pasta WAL ou a pasta. tmp). Em seguida, no Cloud Explorer ou usando comandos do HDFS, tente localizar o arquivo de problema. Normalmente, esse é um arquivo `*-renamePending.json`. (O arquivo de `*-renamePending.json` é um arquivo de diário que é usado para implementar a operação de renomeação atômica no driver WASB. Devido a bugs nessa implementação, esses arquivos podem ser deixados após a falha do processo e assim por diante.) Force-exclui esse arquivo no Cloud Explorer ou usando comandos do HDFS.

Às vezes, também pode haver um arquivo temporário chamado algo como `$$$.$$$` neste local. Você precisa usar o comando HDFS `ls` para ver esse arquivo; Você não pode ver o arquivo no Cloud Explorer. Para excluir esse arquivo, use o comando HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

Depois de executar esses comandos, o HMaster deve iniciar imediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Cenário: nenhum endereço de servidor listado

### <a name="issue"></a>Problema

Você pode ver uma mensagem que indica que a tabela de `hbase: meta` não está online. A execução de `hbck` pode relatar que `hbase: meta table replicaId 0 is not found on any region.` nos logs do HMaster, você poderá ver a mensagem: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Causa

HMaster não pôde inicializar após a reinicialização do HBase.

### <a name="resolution"></a>Resolução

1. No Shell do HBase, insira os seguintes comandos (altere os valores reais conforme aplicável):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Exclua a entrada de `hbase: namespace`. Essa entrada pode ser o mesmo erro que está sendo relatado quando a tabela de `hbase: namespace` é verificada.

1. Reinicie o Active HMaster da interface do usuário do Ambari para ativar o HBase no estado de execução.

1. No Shell do HBase, para abrir todas as tabelas offline, execute o seguinte comando:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Cenário: Java. IO. IOException: TimedOut

### <a name="issue"></a>Problema

HMaster atinge o tempo limite com uma exceção fatal semelhante a: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Causa

Você poderá enfrentar esse problema se tiver muitas tabelas e regiões que não foram liberadas quando você reiniciar os serviços HMasters. O tempo limite é um defeito conhecido com HMaster. As tarefas gerais de inicialização do cluster podem levar muito tempo. O HMaster será desligado se a tabela de namespace ainda não tiver sido atribuída. As tarefas de inicialização demoradas ocorrem onde há grande quantidade de dados não liberados e um tempo limite de cinco minutos não é suficiente.

### <a name="resolution"></a>Resolução

1. Na interface do usuário do Apache Ambari, acesse **HBase** > **configurações**. No arquivo de `hbase-site.xml` personalizado, adicione a seguinte configuração:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Reinicie os serviços necessários (HMaster e possivelmente outros serviços do HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Cenário: reinicializações de servidor de região frequentes

### <a name="issue"></a>Problema

Nós reiniciam periodicamente. Nos logs do servidor de região, você pode ver entradas semelhantes a:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Pausa longa `regionserver` do GC de JVM. A pausa fará com que `regionserver` não respondam e não seja possível enviar uma pulsação de coração para HMaster dentro do 40s de tempo limite de sessão ZK. O HMaster acreditará `regionserver` está inativo e anulará o `regionserver` e será reiniciado.

### <a name="resolution"></a>Resolução

Altere o tempo limite da sessão Zookeeper, não apenas `hbase-site` configuração `zookeeper.session.timeout`, mas também Zookeeper `zoo.cfg` configuração `maxSessionTimeout` precisa ser alterada.

1. Acesse a interface do usuário do Ambari, vá para **> de configuração do HBase – configurações de >** , na seção tempos limite, altere o valor do tempo limite da sessão Zookeeper.

1. Acesse a interface do usuário do Ambari, vá para **configurações do Zookeeper->-> `zoo.cfg`personalizado** , adicione/altere a configuração a seguir. Verifique se o valor é o mesmo que o HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Reinicie os serviços necessários.

---

## <a name="scenario-log-splitting-failure"></a>Cenário: falha de divisão de log

### <a name="issue"></a>Problema

Falha do HMasters ao surgir em um cluster HBase.

### <a name="cause"></a>Causa

Configurações do HDFS e do HBase configuradas incorretamente para uma conta de armazenamento secundária.

### <a name="resolution"></a>Resolução

Defina HBase. rootdir: wasb://@.blob.core.windows.net/hbase e reinicie os serviços em Ambari.

---

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
