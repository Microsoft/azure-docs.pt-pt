---
title: Tecido de serviço Azure CLI- sfctl malha secretvalue
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para obter e apagar os recursos secretos de Malha de Tecido de Serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905957"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Obtenha e elimine os recursos secretos de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o valor especificado do recurso secreto nomeado. |
| list | Enumeraos nomes de todos os valores do recurso secreto especificado. |
| Mostrar | Lista o valor especificado do recurso secreto. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl malha segredo value excluir
Elimina o valor especificado do recurso secreto nomeado.

Elimina o recurso de valor secreto identificado pelo nome. O nome do recurso é tipicamente a versão associada a esse valor. A eliminação falhará se o valor especificado estiver em uso.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome secreto -n [Obrigatório] | O nome do recurso secreto. |
| --versão -v [Obrigatório] | O nome da versão secreta. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-mesh-secretvalue-list"></a>lista de segredos de malha sfctl
Enumeraos nomes de todos os valores do recurso secreto especificado.

Obtém informações sobre todos os recursos secretos do recurso secreto especificado. A informação inclui os nomes dos recursos de valor secreto, mas não os valores reais.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome secreto -n [Obrigatório] | O nome do recurso secreto. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl malha secretvalue show
Lista o valor especificado do recurso secreto.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome secreto -n [Obrigatório] | O nome do recurso secreto. |
| --versão -v [Obrigatório] | O nome da versão secreta. |
| -show-value | Mostre o valor real da versão secreta. |

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