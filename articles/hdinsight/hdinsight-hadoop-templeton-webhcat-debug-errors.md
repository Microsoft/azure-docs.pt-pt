---
title: Compreender e resolver erros do WebHCat no HDInsight - Azure
description: Saiba como lidar com erros comuns devolvidos pelo WebHCat no HDInsight e como resolvê-los.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ce7a21a2b5401c68c6b58bc934fb20b0225ba8ad
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945469"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Understand and resolve errors received from WebHCat on HDInsight (Compreender e resolver erros recebidos do WebHCat no HDInsight)

Saiba mais sobre os erros recebidos ao utilizar o WebHCat com HDInsight e como resolvê-los. O WebHCat é utilizado internamente por ferramentas do lado do cliente, como o Azure PowerShell e as Ferramentas do Lago de Dados para o Estúdio Visual.

## <a name="what-is-webhcat"></a>O que é WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para [HCatalog,](https://cwiki.apache.org/confluence/display/Hive/HCatalog)uma tabela e camada de gestão de armazenamento para Apache Hadoop. O WebHCat é ativado por padrão em clusters HDInsight, e é usado por várias ferramentas para submeter empregos, obter o estado de trabalho, e assim por diante, sem iniciar sessão no cluster.

## <a name="modifying-configuration"></a>Modificação da configuração

Vários dos erros enumerados neste documento ocorrem porque um máximo configurado foi ultrapassado. Quando o passo de resolução mencionar que pode alterar um valor, use Apache Ambari (web ou REST API) para modificar o valor. Para mais informações, consulte [Manage HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Configuração predefinida

Se os seguintes valores predefinidos forem ultrapassados, pode degradar o desempenho do WebHCat ou causar erros:

| Definição | O que faz | Valor predefinido |
| --- | --- | --- |
| [yarn.scheduler.capacit.maximum-applications][maximum-applications] |O número máximo de postos de trabalho que podem estar ativos simultaneamente (pendentes ou em execução) |10,000 |
| [templeton.exec.max-procs][max-procs] |O número máximo de pedidos que podem ser servidos simultaneamente |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |O número de dias em que o histórico de empregos é mantido |7 dias |

## <a name="too-many-requests"></a>Muitos pedidos

**Código de estado HTTP**: 429

| Causa | Resolução |
| --- | --- |
| Excedeu os pedidos simultâneos máximos servidos pelo WebHCat por minuto (padrão 20) |Reduza a sua carga de trabalho para garantir que não submete mais do que o número máximo de pedidos simultâneos ou aumente o limite de pedidos simultâneos modificando `templeton.exec.max-procs` . Para obter mais informações, consulte [modificar a configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Servidor indisponível

**Código de estado HTTP**: 503

| Causa | Resolução |
| --- | --- |
| Este código de estado ocorre geralmente durante o failover entre o HeadNode primário e secundário para o cluster |Espere dois minutos, depois re-tentar a operação. |

## <a name="bad-request-content-could-not-find-job"></a>Mau pedido Conteúdo: Não consegui encontrar emprego

**CÓDIGO DE ESTADO HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Os detalhes do trabalho foram limpos pelo limpador de história do trabalho |O período de retenção por defeito para o histórico de empregos é de 7 dias. O período de retenção por defeito pode ser alterado modificando `mapreduce.jobhistory.max-age-ms` . Para obter mais informações, consulte [modificar a configuração](#modifying-configuration) |
| O trabalho foi morto por causa de um fracasso. |Redação de candidatura por até dois minutos |
| Um ID de trabalho inválido foi usado |Verifique se o ID do trabalho está certo |

## <a name="bad-gateway"></a>Mau portal

**CÓDIGO DE ESTADO HTTP**: 502

| Causa | Resolução |
| --- | --- |
| A recolha interna de lixo está a ocorrer no âmbito do processo WebHCat |Aguarde que a recolha do lixo termine ou reinicie o serviço WebHCat |
| Time out à espera de uma resposta do serviço ResourceManager. Este erro pode ocorrer quando o número de aplicações ativas for o máximo configurado (padrão 10.000) |Aguarde que atualmente os postos de trabalho completem ou aumentem o limite de emprego simultâneo modificando `yarn.scheduler.capacity.maximum-applications` . Para obter mais informações, consulte a secção [de configuração modificante.](#modifying-configuration) |
| Tentar recuperar todos os empregos através da chamada [GET/jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) enquanto `Fields` está definido para `*` |Não recupere *todos os* detalhes do trabalho. Em vez disso, use `jobid` para recuperar detalhes para empregos apenas maiores do que certos postos de trabalho. Ou, não use `Fields` |
| O serviço WebHCat está em baixo durante o failover headNode |Espere dois minutos e relemisça a operação |
| Existem mais de 500 postos de trabalho pendentes submetidos através do WebHCat |Aguarde até que os postos de trabalho pendentes tenham concluído antes de apresentar mais empregos |

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml