---
title: Compreender e resolver erros de WebHCat no HDInsight - Azure
description: Saiba como a about comuns erros devolvidos pelo WebHCat no HDInsight e como resolvê-los.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: cfbd42a67f9c9d6c66df3787b53575dc9e918e35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067976"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Compreender e resolver erros recebidos de WebHCat no HDInsight

Saiba mais sobre erros recebidos quando utiliza o WebHCat com o HDInsight e como resolvê-los. WebHCat é utilizada internamente pelo lado do cliente de ferramentas como o Azure PowerShell e ferramentas do Data Lake para Visual Studio.

## <a name="what-is-webhcat"></a>O que é o WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma tabela e a camada de gestão de armazenamento para o Apache Hadoop. WebHCat é habilitado por padrão nos clusters do HDInsight e é utilizado por várias ferramentas para submeter tarefas, obter o estado da tarefa, etc. sem iniciar sessão para o cluster.

## <a name="modifying-configuration"></a>Modificar configuração

> [!IMPORTANT]  
> Vários dos erros listados neste documento ocorrerem porque foi excedido um máximo configurado. Quando o passo de resolução menciona que é possível alterar um valor, tem de utilizar um dos seguintes procedimentos para efetuar a alteração:

* Para **Windows** clusters: Utilize uma ação de script para configurar o valor durante a criação do cluster. Para obter mais informações, consulte [desenvolver ações de script](hdinsight-hadoop-script-actions-linux.md).

* Para **Linux** clusters: Utilize o Apache Ambari (web ou REST API) para modificar o valor. Para obter mais informações, consulte [gerir o HDInsight com o Apache Ambari](hdinsight-hadoop-manage-ambari.md)


### <a name="default-configuration"></a>Configuração predefinida

Se os seguintes valores predefinidos são excedidos, pode degradar o desempenho de WebHCat ou causar erros:

| Definição | O que faz | Valor predefinido |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |O número máximo de tarefas que podem estar ativas em simultâneo (em execução ou pendente) |10,000 |
| [templeton.exec.max-procs][max-procs] |O número máximo de pedidos que podem ser fornecidos em simultâneo |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |O número de dias que o histórico de tarefas é mantido |7 dias |

## <a name="too-many-requests"></a>Demasiados pedidos

**Código de estado HTTP**: 429

| Causa | Resolução |
| --- | --- |
| Excedeu os máximos pedidos simultâneos servidos pelo WebHCat por minuto (predefinição de 20) |Reduza a carga de trabalho para se certificar de que não submeta mais do que o número máximo de pedidos simultâneos ou aumentar o limite de pedido simultâneo modificando `templeton.exec.max-procs`. Para obter mais informações, consulte [Modifying configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Servidor indisponível

**Código de estado HTTP**: 503

| Causa | Resolução |
| --- | --- |
| Este código de estado geralmente ocorre durante a ativação pós-falha entre o nó principal primário e secundário para o cluster |Aguardar dois minutos, em seguida, repita a operação |

## <a name="bad-request-content-could-not-find-job"></a>Pedido incorreto conteúdo: Não foi possível encontrar a tarefa

**Código de estado HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Detalhes da tarefa foram limpos pelo histórico da tarefa de limpeza |O período de retenção predefinido para o histórico de tarefas é de 7 dias. O período de retenção predefinido pode ser alterado ao modificar `mapreduce.jobhistory.max-age-ms`. Para obter mais informações, consulte [Modifying configuração](#modifying-configuration) |
| Tarefa tiver sido terminada devido a uma ativação pós-falha |Repita a submissão da tarefa para até dois minutos |
| Foi utilizado um ID de tarefa inválida |Verifique se o ID da tarefa está correto |

## <a name="bad-gateway"></a>Gateway inválido

**Código de estado HTTP**: 502

| Causa | Resolução |
| --- | --- |
| Coleta de lixo interno que está a ocorrer dentro do processo de WebHCat |Aguarde pela coleta de lixo seja concluída ou reinicie o serviço de WebHCat |
| Tempo limite à espera de uma resposta do serviço ResourceManager. Este erro pode ocorrer quando o número de aplicativos ativos fica o máximo configurado (predefinição 10 000) |Aguarde tarefas para concluir ou aumentar o limite de trabalhos simultâneos modificando atualmente em execução `yarn.scheduler.capacity.maximum-applications`. Para obter mais informações, consulte a [Modifying configuração](#modifying-configuration) secção. |
| Tentar obter todas as tarefas através do [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) chamada ao `Fields` está definido como `*` |Não obtêm *todos os* detalhes da tarefa. Em alternativa utilize `jobid` para obter detalhes de tarefas apenas maiores do que determinados ID da tarefa. Ou, não utilize `Fields` |
| O serviço de WebHCat está inativo durante a ativação pós-falha de nó principal |Aguardar dois minutos e repita a operação |
| Há mais de 500 tarefas pendentes, submetidas através de WebHCat |Aguarde até que atualmente pendentes tarefas foram concluídas antes de submeter mais tarefas |

[maximum-applications]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
