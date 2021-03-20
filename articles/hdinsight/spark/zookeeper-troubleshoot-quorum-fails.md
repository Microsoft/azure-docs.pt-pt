---
title: O servidor Apache ZooKeeper não consegue formar um quórum em Azure HDInsight
description: O servidor Apache ZooKeeper não consegue formar um quórum em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929202"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>O servidor Apache ZooKeeper não consegue formar um quórum em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para questões relacionadas com zookeepers em clusters Azure HDInsight.

## <a name="symptoms"></a>Sintomas

* Ambos os gestores de recursos vão para o modo de espera
* Os nomes estão ambos em modo de espera
* Os trabalhos de faísca, colmeia e yarn ou consultas de colmeia falham devido a falhas de ligação ao Zookeeper
* Os daemons llap não começam em aglomerados de Faíscas seguros ou seguros de Colmeias interativas

## <a name="sample-log"></a>Registo de amostras

Pode ver uma mensagem de erro semelhante à seguinte nos registos de fios (/var/log/hadoop-yarn/yarn/yarn-yarn*.log nos headnodes):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Questões relacionadas

* Serviços de alta disponibilidade como Yarn, NameNode e Livy podem descer por muitas razões.
* Confirme a partir dos registos que está relacionado com ligações zookeeper
* Certifique-se de que o problema acontece repetidamente (não utilize estas soluções para casos únicos)
* Os empregos podem falhar temporariamente devido a problemas de ligação ao Zookeeper

## <a name="common-causes-for-zookeeper-failure"></a>Causas comuns para o fracasso do Zookeeper

* Alta utilização do CPU nos servidores do zookeeper
  * Na UI Ambari, se você vir quase 100% sustentada utilização de CPU nos servidores do zookeeper, então as sessões de zookeeper abertas durante esse tempo podem expirar e tempo fora
* Os clientes do Zookeeper estão a reportar intervalos frequentes
  * Nos registos de Gestor de Recursos, Namenode e outros, verá intervalos frequentes de ligação ao cliente
  * Isto pode resultar em perda de quórum, falhas frequentes, e outras questões

## <a name="check-for-zookeeper-status"></a>Verifique o estado do zookeeper

* Encontre os servidores zookeeper a partir do ficheiro /etc/hosts ou da Ambari UI
* Execute o seguinte comando
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (ou 2182)  
  * Porto 2181 é a instância apache zookeeper
  * O porto 2182 é utilizado pelo zookeeper HDInsight (para fornecer HA para serviços que não são nativos HA)
  * Se o comando não mostrar saída, significa que os servidores do zookeeper não estão a funcionar.
  * Se os servidores estiverem em execução, o resultado incluirá estáticas de ligações ao cliente e outras estatísticas

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>A carga do CPU atinge picos a cada hora

* Faça login no servidor zookeeper e verifique o /etc/crontab
* Se houver trabalhos de hora a hora a decorrer neste momento, aleatoriamente a hora de início em diferentes servidores do zookeeper.
  
## <a name="purging-old-snapshots"></a>Purgar fotos antigas

* Os zookeepers são configurados para expurgar fotos antigas de auto purga
* Por padrão, os últimos 30 instantâneos são retidos
* O número de instantâneos que são retidos, é controlado pela chave de configuração `autopurge.snapRetainCount` . Esta propriedade pode ser encontrada nos seguintes ficheiros:
  * `/etc/zookeeper/conf/zoo.cfg` para Hadoop zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` para o zookeeper HDInsight
* Definir `autopurge.snapRetainCount` para um valor de 3 e reiniciar os servidores zookeeper
  * Hadoop zookeeper config pode ser atualizado e o serviço pode ser reiniciado através de Ambari
  * Pare e reinicie manualmente o zookeeper HDInsight
    * `sudo lsof -i :2182` vai dar-lhe o iD processo para matar
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Não purgue as imagens manualmente - eliminar instantâneos manualmente pode resultar na perda de dados

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CanceladoKeyException no registo do servidor zookeeper não requer limpeza instantânea

* Esta exceção será vista nos servidores do zookeeper (/var/log/zookeeper/zookeeper-* ou /var/log/hdinsight-zookeeper/zookeeper*
* Esta exceção geralmente significa que o cliente já não está ativo e o servidor é incapaz de enviar uma mensagem
* Esta exceção também indica que o cliente zookeeper está terminando as sessões prematuramente
* Procure os outros sintomas descritos neste documento

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.
- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).