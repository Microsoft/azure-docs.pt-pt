---
title: Compreender e resolver erros do WebHCat no HDInsight - Azure
description: Saiba como lidar com erros comuns devolvidos pelo WebHCat no HDInsight e como resolvê-los.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638855"
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
| Excedeu os pedidos máximos simultâneos servidos pelo WebHCat por minuto (padrão 20) |Reduza a sua carga de trabalho para garantir que não submete mais do que o `templeton.exec.max-procs`número máximo de pedidos simultâneos ou aumente o limite de pedido simultâneo modificando . Para mais informações, consulte [Modificar a configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Servidor indisponível

**Código de estado HTTP**: 503

| Causa | Resolução |
| --- | --- |
| Este código de estado ocorre geralmente durante a falha entre o HeadNode primário e secundário para o cluster |Espere dois minutos, e depois tente novamente a operação. |

## <a name="bad-request-content-could-not-find-job"></a>Conteúdo de pedido mau: Não conseguiu encontrar emprego

**Código de estado HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Os detalhes do trabalho foram limpos pelo limpador de história do trabalho. |O período de retenção padrão para o histórico de empregos é de 7 dias. O período de retenção por `mapreduce.jobhistory.max-age-ms`defeito pode ser alterado modificando . Para mais informações, consulte [Modificar a configuração](#modifying-configuration) |
| O trabalho foi morto devido a uma falha. |Retentar a submissão de emprego por até dois minutos |
| Foi usado um id de trabalho inválido |Verifique se o ID do trabalho está correto |

## <a name="bad-gateway"></a>Mau portal

**Código de estado HTTP**: 502

| Causa | Resolução |
| --- | --- |
| A recolha interna de lixo está a ocorrer no âmbito do processo WebHCat |Aguarde a recolha de lixo para terminar ou reiniciar o serviço WebHCat |
| Tempo à espera de uma resposta do serviço ResourceManager. Este erro pode ocorrer quando o número de aplicações ativas vai para o máximo configurado (padrão 10.000) |Aguarde que os postos de trabalho atualmente em `yarn.scheduler.capacity.maximum-applications`funcionamento completem ou aumentem o limite de emprego simultâneo modificando . Para mais informações, consulte a secção [de configuração De modificação.](#modifying-configuration) |
| Tentando recuperar todos os empregos através `Fields` da chamada [GET/jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) enquanto está definido para`*` |Não recupere *todos os* detalhes do trabalho. Em `jobid` vez disso, utilize para recuperar detalhes para trabalhos apenas maiores do que certos id de trabalho. Ou, não use`Fields` |
| O serviço WebHCat está em baixo durante a falha do HeadNode |Aguarde dois minutos e tente novamente a operação |
| Há mais de 500 postos de trabalho pendentes submetidos através do WebHCat |Aguarde até que os postos de trabalho pendentes tenham concluído antes de apresentar mais empregos |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
