---
title: Compreender e resolver erros do WebHCat no HDInsight - Azure
description: Saiba como lidar com erros comuns devolvidos pelo WebHCat no HDInsight e como resolvê-los.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 40d49d156b76db5e02ec48defbb82ed60819c478
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651104"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Understand and resolve errors received from WebHCat on HDInsight (Compreender e resolver erros recebidos do WebHCat no HDInsight)

Saiba mais sobre os erros recebidos ao utilizar o WebHCat com o HDInsight e como resolvê-los. WebHCat é usado internamente por ferramentas do lado do cliente, como o Azure PowerShell e as Ferramentas data lake para estúdio visual.

## <a name="what-is-webhcat"></a>O que é WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para [HCatalog,](https://cwiki.apache.org/confluence/display/Hive/HCatalog)uma tabela e camada de gestão de armazenamento para Apache Hadoop. O WebHCat é ativado por padrão nos clusters HDInsight, e é usado por várias ferramentas para submeter empregos, obter o estatuto de emprego, e assim por diante, sem iniciar sessão no cluster.

## <a name="modifying-configuration"></a>Modificação da configuração

Vários dos erros listados neste documento ocorrem porque um máximo configurado foi ultrapassado. Quando o passo da resolução mencionar que pode alterar um valor, utilize o Apache Ambari (Web ou REST API) para modificar o valor. Para mais informações, consulte [Manage HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Configuração predefinida

Se os seguintes valores predefinidos forem ultrapassados, pode degradar o desempenho do WebHCat ou causar erros:

| Definição | O que faz | Valor predefinido |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |O número máximo de postos de trabalho que podem ser ativos simultaneamente (pendentes ou em funcionamento) |10,000 |
| [templeton.exec.max-procs][max-procs] |O número máximo de pedidos que podem ser servidos simultaneamente |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |O número de dias em que o histórico de empregos são mantidos |7 dias |

## <a name="too-many-requests"></a>Muitos pedidos

**Código de estado HTTP**: 429

| Causa | Resolução |
| --- | --- |
| Excedeu os pedidos máximos simultâneos servidos pelo WebHCat por minuto (padrão 20) |Reduza a sua carga de trabalho para garantir que não submete mais do que o número máximo de pedidos simultâneos ou aumente o limite de pedido simultâneo modificando `templeton.exec.max-procs` . Para mais informações, consulte [Modificar a configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Servidor indisponível

**Código de estado HTTP**: 503

| Causa | Resolução |
| --- | --- |
| Este código de estado ocorre geralmente durante a falha entre o HeadNode primário e secundário para o cluster |Espere dois minutos, e depois tente novamente a operação. |

## <a name="bad-request-content-could-not-find-job"></a>Conteúdo de pedido mau: Não conseguiu encontrar emprego

**Código de estado HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Os detalhes do trabalho foram limpos pelo limpador de história do trabalho. |O período de retenção padrão para o histórico de empregos é de 7 dias. O período de retenção por defeito pode ser alterado modificando `mapreduce.jobhistory.max-age-ms` . Para mais informações, consulte [Modificar a configuração](#modifying-configuration) |
| O trabalho foi morto por causa de um fracasso. |Retentar a submissão de emprego por até dois minutos |
| Foi usado um id de trabalho inválido |Verifique se o ID do trabalho está correto |

## <a name="bad-gateway"></a>Mau portal

**Código de estado HTTP**: 502

| Causa | Resolução |
| --- | --- |
| A recolha interna de lixo está a ocorrer no âmbito do processo WebHCat |Aguarde a recolha de lixo para terminar ou reiniciar o serviço WebHCat |
| Tempo à espera de uma resposta do serviço ResourceManager. Este erro pode ocorrer quando o número de aplicações ativas vai para o máximo configurado (padrão 10.000) |Aguarde que os postos de trabalho atualmente em funcionamento completem ou aumentem o limite de emprego simultâneo modificando `yarn.scheduler.capacity.maximum-applications` . Para mais informações, consulte a secção [de configuração De modificação.](#modifying-configuration) |
| Tentando recuperar todos os empregos através da chamada [GET/jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) enquanto `Fields` está definido para`*` |Não recupere *todos os* detalhes do trabalho. Em vez disso, utilize `jobid` para recuperar detalhes para trabalhos apenas maiores do que certos id de trabalho. Ou, não use`Fields` |
| O serviço WebHCat está em baixo durante a falha do HeadNode |Aguarde dois minutos e tente novamente a operação |
| Há mais de 500 postos de trabalho pendentes submetidos através do WebHCat |Aguarde até que os postos de trabalho pendentes tenham concluído antes de apresentar mais empregos |

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml