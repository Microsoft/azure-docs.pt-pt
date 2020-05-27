---
title: Servidor Apache ZooKeeper falha em formar quórum no Azure HDInsight
description: Servidor Apache ZooKeeper falha em formar quórum no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: dc93121d7565b95b9bd604160028659f3a741b0c
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860499"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Servidor Apache ZooKeeper falha em formar quórum no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para questões relacionadas com zookeepers em clusters Azure HDInsight.

## <a name="symptoms"></a>Sintomas

* Ambos os gestores de recursos vão para o modo de espera
* Nomes são ambos em modo de espera
* Trabalhos de faísca, colmeia e fios ou consultas de colmeia falham devido a falhas de ligação zookeeper
* Os daemons llap não conseguem começar em aglomerados de Faísca seletiva seletiva seletiva ou segura

## <a name="sample-log"></a>Registo de amostras

Pode ver uma mensagem de erro semelhante a:

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
* Certifique-se de que o problema acontece repetidamente (não utilize estas soluções para um caso único)
* Empregos podem falhar temporariamente devido a problemas de ligação ao Zookeeper

## <a name="common-causes-for-zookeeper-failure"></a>Causas comuns para falha do Zookeeper

* Alta utilização de CPU nos servidores do zookeeper
  * Na UI Ambari, se você vir o uso de CPU sustentado quase 100% sustentado nos servidores do zookeeper, então as sessões de zookeeper abertas durante esse tempo podem expirar e tempo fora
* Os clientes do Zookeeper estão a reportar intervalos frequentes
  * Nos registos para Gestor de Recursos, Namenode e outros, você verá tempos de ligação frequentes do cliente
  * Isto pode resultar em perda de quórum, falhas frequentes, e outras questões

## <a name="check-for-zookeeper-status"></a>Verifique o estado do zookeeper

* Encontre os servidores do zookeeper a partir do ficheiro /etc/anfitriões ou da Ambari UI
* Execute o seguinte comando
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181`(ou 2182)  
  * Porto 2181 é o caso apache zookeeper
  * A porta 2182 é utilizada pelo zookeeper HDInsight (para fornecer HA para serviços que não são nativos de HA)
  * Se o comando não mostrar saída, significa que os servidores do zookeeper não estão a funcionar
  * Se os servidores estiverem em execução, o resultado incluirá estática de ligações ao cliente e outras estatísticas

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

## <a name="cpu-load-peaks-up-every-hour"></a>Carga cpu atinge picos a cada hora

* Inicie sessão no servidor zookeeper e verifique o /etc/crontab
* Se houver trabalhos de hora em funcionamento neste momento, randomize a hora de início em diferentes servidores de zookeeper.
  
## <a name="purging-old-snapshots"></a>Purgando fotos antigas

* Os zookeepers estão configurados para purgar automaticamente fotos antigas
* Por padrão, os últimos 30 instantâneos são retidos
* O número de instantâneos retidos é controlado pela tecla de configuração `autopurge.snapRetainCount` . Esta propriedade pode ser encontrada nos seguintes ficheiros:
  * `/etc/zookeeper/conf/zoo.cfg`para Hadoop zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg`para o zookeeper HDInsight
* Definir `autopurge.snapRetainCount` para um valor de 3 e reiniciar os servidores do zookeeper
  * Hadoop zookeeper config pode ser atualizado e o serviço pode ser reiniciado através de Ambari
  * Parar e reiniciar manualmente o zookeeper HDInsight
    * `sudo lsof -i :2182`vai dar-lhe o processo ID para matar
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Não expurgar manualmente as imagens - apagar instantâneos manualmente pode resultar na perda de dados

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CanceladaExcepção no registo do servidor do zookeeper não requer limpeza instantânea

* Esta exceção geralmente significa que o cliente já não está ativo e o servidor é incapaz de enviar uma mensagem
* Esta exceção também indica que o cliente do zookeeper está terminando as sessões prematuramente
* Procure os outros sintomas descritos neste documento

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.
- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)