---
title: Réplica de serviço de malha de malha de serviço Azure Fabric CLI-sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para obter detalhes de réplica para um recurso de aplicação.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245726"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtenha detalhes de réplica e liste réplicas de um determinado serviço num recurso de aplicação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| lista | Lista todas as réplicas de um serviço. |
| Mostrar | Obtém a réplica dada do serviço de uma aplicação. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl rede serviço-replica lista
Lista todas as réplicas de um serviço.

Obtém a informação sobre todas as réplicas de um serviço. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --- nome de aplicação --nome de aplicação [Obrigatório] | O nome da aplicação. |
| --nome de serviço [Obrigatório] | O nome do serviço. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl malha serviço-réplica show
Obtém a réplica dada do serviço de uma aplicação.

Obtém a informação sobre a réplica de serviço com o nome próprio. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --- nome de aplicação --nome de aplicação [Obrigatório] | O nome da aplicação. |
| --nome -n [Obrigatório] | O nome da réplica de serviço. |
| --nome de serviço [Obrigatório] | O nome do serviço. |

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
