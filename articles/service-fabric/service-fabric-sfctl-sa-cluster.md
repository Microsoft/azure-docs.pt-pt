---
title: CLI do Azure Service Fabric-sfctl SA-cluster | Microsoft Docs
description: Descreve os comandos de Cluster Service Fabric CLI sfctl autônomos.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 902ebab5dc12d7649edd0ed6e594e663e5332ce3
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035232"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gerenciar clusters Service Fabric autônomos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| configuração | Obtenha a configuração de Cluster Service Fabric autônomo. |
| config-upgrade | Comece a atualizar a configuração de um Cluster Service Fabric autônomo. |
| atualização-status | Obtenha o status de atualização da configuração do cluster de um Cluster Service Fabric autônomo. |

## <a name="sfctl-sa-cluster-config"></a>sfctl SA – configuração de cluster
Obtenha a configuração de Cluster Service Fabric autônomo.

A configuração de cluster contém as propriedades do cluster que incluem diferentes tipos de nó no cluster, configurações de segurança, topologias de domínio de atualização e de falha, etc.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --configuração-API-Version [obrigatório] | A versão da API da configuração JSON do cluster autônomo. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl SA – configuração do cluster-atualização
Comece a atualizar a configuração de um Cluster Service Fabric autônomo.

Valide os parâmetros de atualização de configuração fornecidos e comece a atualizar a configuração do cluster se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-config [obrigatório] | A configuração do cluster. |
| --Application-Health-Policies | Dicionário codificado em JSON de pares de nome do tipo de aplicativo e percentual máximo não íntegro antes de gerar erro. |
| --delta-unhealthy-nodes | A porcentagem máxima permitida de degradação da integridade Delta durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --health-check-retry | O período de tempo entre as tentativas de realizar verificações de integridade se o aplicativo ou o cluster não estiver íntegro.  PT0H0M0S\: padrão. |
| --health-check-stable | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização prossiga para o próximo domínio de atualização.  PT0H0M0S\: padrão. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o processo de verificações de integridade.  PT0H0M0S\: padrão. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --aplicativos não íntegros | A porcentagem máxima permitida de aplicativos não íntegros durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --Nós não íntegros | A porcentagem máxima permitida de nós não íntegros durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --upgrade-domain-delta-unhealthy-nodes | A porcentagem máxima permitida da degradação da integridade Delta do domínio de atualização durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --tempo limite do domínio de atualização | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a falha seja executada.  PT0H0M0S\: padrão. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --upgrade-timeout | O tempo durante o qual a atualização geral deve ser concluída antes de FailureAction ser executada.  PT0H0M0S\: padrão. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Iniciar uma atualização de configuração de cluster

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl SA – atualização do cluster-status
Obtenha o status de atualização da configuração do cluster de um Cluster Service Fabric autônomo.

Obtenha os detalhes do status de atualização da configuração de cluster de um Cluster Service Fabric autônomo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).