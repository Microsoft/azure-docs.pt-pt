---
title: Azure Service Fabric CLI-sfctl mesh secretvalue
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para obter e apagar recursos secretos de malha de tecido de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245777"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Obter e apagar recursos de valor secreto de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o valor especificado do recurso secreto nomeado. |
| lista | Lista os nomes de todos os valores do recurso secreto especificado. |
| Mostrar | Lista o valor especificado do recurso secreto. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
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
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl lista de valores secretos de malha
Lista os nomes de todos os valores do recurso secreto especificado.

Obtém informações sobre todos os recursos secretos do recurso secreto especificado. A informação inclui os nomes dos recursos de valor secreto, mas não os valores reais.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome secreto -n [Obrigatório] | O nome do recurso secreto. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl malha secretvalue show
Lista o valor especificado do recurso secreto.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome secreto -n [Obrigatório] | O nome do recurso secreto. |
| --versão -v [Obrigatório] | O nome da versão secreta. |
| --valor do espetáculo | Mostre o valor real da versão secreta. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
