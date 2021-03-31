---
title: Azure Service Fabric CLI-sfctl sa-cluster
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para gerir agrupamentos autónomos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7e9f4b55945afc04e5c826b26632d68cb75e502f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260267"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gerir clusters de tecido de serviço autónomos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| config | Obtenha a configuração de cluster autónomo do Tecido de Serviço. |
| config-upgrade | Inicie a atualização da configuração de um cluster autónomo de Tecido de Serviço. |
| estado de atualização | Obtenha o estado de atualização da configuração do cluster de um cluster autónomo do Tecido de Serviço. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Obtenha a configuração de cluster autónomo do Tecido de Serviço.

A configuração do cluster contém propriedades do cluster que incluem diferentes tipos de nós no cluster, configurações de segurança, falha e topologias de domínio de upgrade, etc.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --configuração-versão api[Necessária] | A versão API da configuração de json cluster autónomo. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Inicie a atualização da configuração de um cluster autónomo de Tecido de Serviço.

Valide os parâmetros de atualização de configuração fornecidos e inicie a atualização da configuração do cluster se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --cluster-config [Obrigatório] | A configuração do cluster. |
| --aplicações-políticas de saúde | JSON codificava o dicionário de pares de nomes do tipo de aplicação e a percentagem máxima não saudável antes de elevar o erro. |
| --delta-não-saudável-nódoas | A percentagem máxima permitida de degradação da saúde delta durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --verificação da saúde-redação | O período de tempo entre tentativas de efetuar controlos de saúde se a aplicação ou o agrupamento não forem saudáveis.  \:PT0H0M0s predefinidos. |
| --saúde-check-estável | O tempo que a aplicação ou cluster deve permanecer saudável antes da atualização avançar para o próximo domínio de atualização.  \:PT0H0M0s predefinidos. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-espera | O tempo de espera após completar um domínio de upgrade antes de iniciar o processo de verificação de saúde.  \:PT0H0M0s predefinidos. |
| --timeout -t | Padrão \: 60. |
| --aplicações pouco saudáveis | A percentagem máxima permitida de aplicações não saudáveis durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --nó-insalubres | A percentagem máxima permitida de nós não saudáveis durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --upgrade-domínio-delta-unhealthy-nodes | A percentagem máxima permitida de upgrade do domínio delta saúde degradação durante a atualização. Os valores permitidos são valores inteiros de zero a 100. |
| --upgrade-tempo limite de domínio | O tempo que cada domínio de atualização tem de completar antes da execução do FailureAction.  \:PT0H0M0s predefinidos. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tempo limite | O tempo que a atualização global tem de ser concluída antes da execução do FailureAction.  \:PT0H0M0s predefinidos. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

### <a name="examples"></a>Exemplos

Inicie uma atualização de configuração de cluster
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Obtenha o estado de atualização da configuração do cluster de um cluster autónomo do Tecido de Serviço.

Obtenha os detalhes do estado de atualização de configuração do cluster de um cluster autónomo do Tecido de Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
