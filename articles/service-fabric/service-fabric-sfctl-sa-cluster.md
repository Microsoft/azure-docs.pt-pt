---
title: Cluster do Azure Service Fabric CLI - sfctl sa-| Documentos da Microsoft
description: Descreve os comandos de cluster do Service Fabric CLI sfctl autónomo.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a652439729e538b3ce2545ab3b09284e6645ce9d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556394"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gerir clusters autónomos do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| config | Obter a configuração de cluster autónomo do Service Fabric. |
| config-upgrade | Começar a atualizar a configuração de um cluster autónomo do Service Fabric. |
| upgrade-status | Obter o estado de atualização de configuração de cluster de um cluster autónomo do Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>configuração de cluster de sa de sfctl
Obter a configuração de cluster autónomo do Service Fabric.

A configuração de cluster contém as propriedades do cluster, que incluem os tipos de nó diferente no cluster, configurações de segurança, falhas e topologias de domínio de atualização, etc.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – versão de api de configuração [necessário] | A versão de API de configuração do json de cluster autónomo. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>atualização de configuração do sa-cluster sfctl
Começar a atualizar a configuração de um cluster autónomo do Service Fabric.

Valide os parâmetros de atualização de configuração fornecidos e começar a atualizar a configuração do cluster, se os parâmetros são válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-config            [Required] | A configuração do cluster. |
| – políticas de estado de funcionamento de aplicações | JSON com a codificação de dicionário de pares de nome de tipo de aplicação e a percentagem máxima em mau estado de funcionamento antes de gerar o erro. |
| --delta-unhealthy-nodes | O máximo permitido de percentagem de degradação de estado de funcionamento de delta durante a atualização. Valores permitidos são valores de número inteiro entre zero e 100. |
| --health-check-retry | O período de tempo entre tentativas para realizar verificações de estado de funcionamento se o aplicativo ou o cluster não está em bom estado.  Default\: PT0H0M0S. |
| --health-check-stable | A quantidade de tempo que a aplicação ou o cluster deve permanecer em bom estado antes que a atualização prossiga para o domínio de atualização seguinte.  Default\: PT0H0M0S. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o estado de funcionamento verifica o processo.  Default\: PT0H0M0S. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --unhealthy-applications | O máximo permitido porcentagem de aplicativos com problemas durante a atualização. Valores permitidos são valores de número inteiro entre zero e 100. |
| --unhealthy-nodes | O máximo permitido de percentagem de nós de mau estado de funcionamento durante a atualização. Valores permitidos são valores de número inteiro entre zero e 100. |
| --upgrade-domain-delta-unhealthy-nodes | O máximo permitido de percentagem de degradação de estado de funcionamento de delta do domínio de atualização durante a atualização. Valores permitidos são valores de número inteiro entre zero e 100. |
| --upgrade-domain-timeout | A quantidade de tempo de cada domínio de atualização tem de concluir antes de ser executada FailureAction.  Default\: PT0H0M0S. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --upgrade-timeout | A quantidade de tempo a atualização global tem de concluir antes de ser executada FailureAction.  Default\: PT0H0M0S. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

### <a name="examples"></a>Exemplos

Iniciar uma atualização de configuração de cluster

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>Estado de atualização do cluster de sa sfctl
Obter o estado de atualização de configuração de cluster de um cluster autónomo do Service Fabric.

Obter a configuração de cluster detalhes de estado de atualização de um cluster autónomo do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).