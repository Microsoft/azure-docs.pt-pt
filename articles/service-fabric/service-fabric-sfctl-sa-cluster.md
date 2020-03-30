---
title: Tecido de serviço Azure CLI-sfctl sa-cluster
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerir clusters autónomos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904920"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gerencie os clusters de tecido de serviço autónomos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| config | Obtenha a configuração autónoma do cluster service Fabric. |
| config-upgrade | Comece a atualizar a configuração de um cluster autónomo de Tecido de Serviço. |
| estado de atualização | Obtenha o estado de upgrade de configuração do cluster de um cluster autónomo de Tecido de Serviço. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Obtenha a configuração autónoma do cluster service Fabric.

A configuração do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, configurações de segurança, falhas e topoologias de domínio de upgrade, etc.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --configuração-api-versão [Obrigatório] | A versão API da configuração de json cluster autónomo. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Comece a atualizar a configuração de um cluster autónomo de Tecido de Serviço.

Valide os parâmetros de atualização de configuração fornecidos e comece a atualizar a configuração do cluster se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-config [Obrigatório] | A configuração do cluster. |
| --aplicação-políticas de saúde | JSON codificadicionário codificado de pares de nome tipo de aplicação e percentagem máxima insalubre antes de aumentar o erro. |
| --delta-insalubre-nódosos | A percentagem máxima permitida de degradação da saúde delta durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --saúde-check-retry | O período de tempo entre as tentativas de realização de verificações de saúde se a aplicação ou o cluster não forem saudáveis.  PT0H0M0s padrão.\: |
| --saúde-check-estável | O tempo que a aplicação ou o cluster devem manter-se saudáveis antes da atualização passar para o próximo domínio de atualização.  PT0H0M0s padrão.\: <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-wait | O tempo de espera após completar um domínio de atualização antes de iniciar o processo de verificação de saúde.  PT0H0M0s padrão.\: |
| --timeout -t | Padrão\: 60. |
| --aplicações pouco saudáveis | A percentagem máxima permitida de aplicações não saudáveis durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| ---não-saudáveis-nódosos | A percentagem máxima permitida de nódosos não saudáveis durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| -upgrade-domínio-delta-insalubre-nódosos | A percentagem máxima permitida de degradação delta do domínio de upgrade durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --upgrade-tempo de domínio | O tempo que cada domínio de atualização tem de completar antes da execução da Ação de Falha.  PT0H0M0s padrão.\: <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-timeout | O tempo que a atualização global tem de completar antes da execução da FailAction.  PT0H0M0s padrão.\: <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

### <a name="examples"></a>Exemplos

Inicie uma atualização de configuração de cluster
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Obtenha o estado de upgrade de configuração do cluster de um cluster autónomo de Tecido de Serviço.

Obtenha os detalhes do estado de upgrade de configuração do cluster de um cluster autónomo de Tecido de Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).