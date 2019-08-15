---
title: O Apache HBase Master não é iniciado no Azure HDInsight
description: O Apache HBase Master (HMaster) não é iniciado no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935409"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>O Apache HBase Master (HMaster) não é iniciado no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Cenário: Falha de renomeação atômica

### <a name="issue"></a>Problema

Arquivos inesperados identificados durante o processo de inicialização.

### <a name="cause"></a>Causa

Durante o processo de inicialização, o HMaster executa muitas etapas de inicialização, incluindo a movimentação de dados da pasta Scratch (. tmp) para a pasta de dados. HMaster também examina a pasta WALs (logs de gravação antecipada) para ver se há servidores de região mortos. Durante todas essas situações, ele faz um comando `list` básico nessas pastas. Se, a qualquer momento, ele vir um arquivo inesperado em qualquer uma dessas pastas, ele gerará uma exceção e, portanto, não será iniciado.

### <a name="resolution"></a>Resolução

Nessa situação, verifique a pilha de chamadas para ver qual pasta pode estar causando problema (por exemplo, a pasta WALs ou a pasta. tmp). Em seguida, via Cloud Explorer ou por meio de comandos HDFS para localizar o arquivo problemático. O arquivo de problema geralmente é `*-renamePending.json` um arquivo (um arquivo de diário usado para implementar a operação de renomeação atômica no driver WASB). Devido a bugs nessa implementação, esses arquivos podem ser deixados em casos de falha do processo. Forçar exclusão deste arquivo por meio do Cloud Explorer. Além disso, pode haver um arquivo temporário da natureza $ neste local. O arquivo não pode ser visto por meio do Cloud Explorer e `ls` somente por meio do comando HDFS. Você pode usar o comando `hdfs dfs -rm //\$\$\$.\$\$\$` HDFS para excluir esse arquivo.

Depois que o arquivo de problema tiver sido removido, o HMaster deverá ser iniciado imediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Cenário: Nenhum endereço de servidor listado

### <a name="issue"></a>Problema

HMaster log mostra uma mensagem de erro semelhante a "nenhum endereço de servidor listado no HBase: meta para a região xxx".

### <a name="cause"></a>Causa

HMaster não pôde inicializar após a reinicialização do HBase.

### <a name="resolution"></a>Resolução

1. Execute os seguintes comandos no Shell do HBase (altere os valores reais conforme aplicável):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Exclua a entrada do HBase: namespace, pois o mesmo erro pode ser relatado durante a verificação da tabela HBase: namespace.

1. Reinicie o Active HMaster da interface do usuário do Ambari para ativar o HBase no estado de execução.

1. Execute o seguinte comando no Shell do HBase para abrir todas as tabelas offline:

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Cenário: Java. IO. IOException: TimedOut

### <a name="issue"></a>Problema

HMaster o tempo limite com exceção fatal `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`, como.

### <a name="cause"></a>Causa

O tempo limite é um defeito conhecido com HMaster. As tarefas gerais de inicialização do cluster podem levar muito tempo. O HMaster será desligado se a tabela de namespace ainda não tiver sido atribuída. As tarefas de inicialização demoradas ocorrem onde há grande quantidade de dados não liberados e um tempo limite de cinco minutos não é suficiente.

### <a name="resolution"></a>Resolução

1. Acesse a interface do usuário do Ambari, vá para configurações de > `hbase-site.xml` do HBase, em Personalizar, adicione a seguinte configuração:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Reinicie os serviços necessários (principalmente HMaster e possivelmente outros serviços do HBase).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Cenário: Reinicializações frequentes de regionserver

### <a name="issue"></a>Problema

Nós reiniciam periodicamente. Nos logs do regionserver, você pode ver entradas semelhantes a:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Longa pausa do GC do regionserver JVM. A pausa fará com que o regionserver não responda e não consiga enviar uma pulsação de coração para HMaster no tempo limite de sessão ZK 40s. HMaster acreditará que o regionserver está inativo e anulará o regionserver e reiniciará.

### <a name="resolution"></a>Resolução

Altere o tempo limite da sessão Zookeeper, não apenas a configuração `zookeeper.session.timeout` do HBase-site, mas também `maxSessionTimeout` a configuração do Zookeeper Zoo. cfg precisa ser alterada.

1. Acesse a interface do usuário do Ambari, vá para **> de configuração do HBase – configurações de >** , na seção tempos limite, altere o valor do tempo limite da sessão Zookeeper.

1. Acesse a interface do usuário do Ambari, vá para **configurações do Zookeeper->->** o Zoo. cfg personalizado, adicione/altere a configuração a seguir. Verifique se o valor é o mesmo que o `zookeeper.session.timeout`HBase.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Reinicie os serviços necessários.

---

## <a name="scenario-log-splitting-failure"></a>Cenário: Falha na divisão de log

### <a name="issue"></a>Problema

Falha do HMasters ao surgir em um cluster HBase.

### <a name="cause"></a>Causa

Configurações do HDFS e do HBase configuradas incorretamente para uma conta de armazenamento secundária.

### <a name="resolution"></a>Resolução

Defina HBase. rootdir: wasb://@.blob.core.windows.net/hbase e reinicie os serviços em Ambari.

---

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
