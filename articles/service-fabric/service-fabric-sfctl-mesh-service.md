---
title: Serviço azure tecido CLI-serviço de malha sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para obter detalhes de serviço para um recurso de aplicação.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905936"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Obtenha detalhes de serviço e serviços de lista de um recurso de aplicação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| list | Lista todos os recursos de serviço. |
| Mostrar | Obtém o recurso de serviço com o nome dado. |

## <a name="sfctl-mesh-service-list"></a>lista de serviço de malha sfctl
Lista todos os recursos de serviço.

Obtém a informação sobre todos os serviços de um recurso de aplicação. As informações incluem a descrição e outras propriedades do Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação --nome da aplicação [Obrigatório] | O nome da aplicação. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-mesh-service-show"></a>sfctl malha show de serviço
Obtém o recurso de serviço com o nome dado.

Obtém a informação sobre o recurso de serviço com o nome dado. As informações incluem a descrição e outras propriedades do Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação --nome da aplicação [Obrigatório] | O nome da aplicação. |
| --nome -n [Obrigatório] | O nome do serviço. |

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