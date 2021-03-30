---
title: Serviço Azure Tecido CLI-sfctl serviço de malha
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para obter detalhes de serviço para um recurso de aplicação.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 68ea876d9951b49a6683cc74df8b9107fd942e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245675"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Obtenha detalhes de serviço e serviços de lista de um recurso de aplicação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| lista | Lista todos os recursos de serviço. |
| Mostrar | Obtém o recurso de serviço com o nome próprio. |

## <a name="sfctl-mesh-service-list"></a>lista de serviços de malha sfctl
Lista todos os recursos de serviço.

Obtém a informação sobre todos os serviços de um recurso de aplicação. As informações incluem a descrição e outras propriedades do Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --- nome de aplicação --nome de aplicação [Obrigatório] | O nome da aplicação. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-service-show"></a>sfctl show de serviço de malha
Obtém o recurso de serviço com o nome próprio.

Obtém a informação sobre o recurso serviço com o nome próprio. As informações incluem a descrição e outras propriedades do Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --- nome de aplicação --nome de aplicação [Obrigatório] | O nome da aplicação. |
| --nome -n [Obrigatório] | O nome do serviço. |

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
