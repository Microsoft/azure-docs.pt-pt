---
title: Entender e resolver erros do WebHCat no HDInsight – Azure
description: Saiba mais sobre os erros comuns retornados pelo WebHCat no HDInsight e como resolvê-los.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638855"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Entender e resolver erros recebidos do WebHCat no HDInsight

Saiba mais sobre os erros recebidos ao usar o WebHCat com o HDInsight e como resolvê-los. O WebHCat é usado internamente pelas ferramentas do lado do cliente, como Azure PowerShell e as ferramentas de Data Lake para o Visual Studio.

## <a name="what-is-webhcat"></a>O que é WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma tabela e uma camada de gerenciamento de armazenamento para Apache Hadoop. O WebHCat é habilitado por padrão em clusters HDInsight e é usado por várias ferramentas para enviar trabalhos, obter o status do trabalho e assim por diante, sem fazer logon no cluster.

## <a name="modifying-configuration"></a>Modificando configuração

Vários dos erros listados neste documento ocorrem porque um máximo configurado foi excedido. Quando a etapa de resolução mencionar que você pode alterar um valor, use o Apache Ambari (Web ou API REST) para modificar o valor. Para obter mais informações, consulte [gerenciar o HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Configuração padrão

Se os valores padrão a seguir forem excedidos, isso poderá prejudicar o desempenho do WebHCat ou causar erros:

| Definição | O que faz | Valor predefinido |
| --- | --- | --- |
| [yarn. scheduler. Capacity. Maximum-Applications][maximum-applications] |O número máximo de trabalhos que podem estar ativos simultaneamente (pendente ou em execução) |10,000 |
| [Templeton. exec. Max-procs][max-procs] |O número máximo de solicitações que podem ser atendidas simultaneamente |20 |
| [MapReduce. jobhistory. Max-age-MS][max-age-ms] |O número de dias que o histórico de trabalho é retido |7 dias |

## <a name="too-many-requests"></a>Número excessivo de solicitações

**Código de status http**: 429

| Causa | Resolução |
| --- | --- |
| Você excedeu o máximo de solicitações simultâneas atendidas por WebHCat por minuto (padrão 20) |Reduza sua carga de trabalho para garantir que você não envie mais do que o número máximo de solicitações simultâneas ou aumente o limite de solicitações simultâneas modificando `templeton.exec.max-procs`. Para obter mais informações, consulte [modificando a configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Servidor não disponível

**Código de status http**: 503

| Causa | Resolução |
| --- | --- |
| Esse código de status geralmente ocorre durante o failover entre o cabeçalho primário e o secundário para o cluster |Aguarde dois minutos e repita a operação |

## <a name="bad-request-content-could-not-find-job"></a>Conteúdo de solicitação inválida: não foi possível encontrar o trabalho

**Código de status http**: 400

| Causa | Resolução |
| --- | --- |
| Os detalhes do trabalho foram limpos pelo limpador de histórico de trabalhos |O período de retenção padrão para o histórico de trabalhos é de 7 dias. O período de retenção padrão pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Para obter mais informações, consulte [modificando a configuração](#modifying-configuration) |
| O trabalho foi encerrado devido a um failover |Repita o envio do trabalho por até dois minutos |
| Uma ID de trabalho inválida foi usada |Verifique se a ID do trabalho está correta |

## <a name="bad-gateway"></a>Gateway inadequado

**Código de status http**: 502

| Causa | Resolução |
| --- | --- |
| A coleta de lixo interna está ocorrendo no processo WebHCat |Aguarde a conclusão da coleta de lixo ou reinicie o serviço WebHCat |
| Tempo limite atingido ao aguardar uma resposta do serviço ResourceManager. Esse erro pode ocorrer quando o número de aplicativos ativos atinge o máximo configurado (padrão 10.000) |Aguarde até que os trabalhos em execução no momento sejam concluídos ou aumente o limite de trabalho simultâneo modificando `yarn.scheduler.capacity.maximum-applications`. Para obter mais informações, consulte a seção [modificando a configuração](#modifying-configuration) . |
| Tentar recuperar todos os trabalhos por meio da chamada [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) enquanto `Fields` está definido como `*` |Não recupere *todos os* detalhes do trabalho. Em vez disso, use `jobid` para recuperar detalhes de trabalhos somente maiores que determinada ID de trabalho. Ou, não use `Fields` |
| O serviço WebHCat está inoperante durante o failover do cabeçalho |Aguarde dois minutos e tente a operação novamente |
| Há mais de 500 trabalhos pendentes enviados por meio do WebHCat |Aguarde até que os trabalhos pendentes no momento sejam concluídos antes de enviar mais trabalhos |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
