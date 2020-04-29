---
title: Azure Service Fabric CLI-sfctl malha service-replica
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para obter detalhes de réplica para recursos de aplicação.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905948"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtenha detalhes de réplica e liste réplicas de um determinado serviço num recurso de aplicação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| list | Lista todas as réplicas de um serviço. |
| Mostrar | Obtém a réplica dada do serviço de uma aplicação. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl malha lista de réplicas de serviço
Lista todas as réplicas de um serviço.

Obtém a informação sobre todas as réplicas de um serviço. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação --nome da aplicação [Obrigatório] | O nome da aplicação. |
| --nome do serviço [Obrigatório] | O nome do serviço. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl malha service-replica show
Obtém a réplica dada do serviço de uma aplicação.

Obtém a informação sobre a réplica do serviço com o nome dado. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação --nome da aplicação [Obrigatório] | O nome da aplicação. |
| --nome -n [Obrigatório] | O nome da réplica de serviço. |
| --nome do serviço [Obrigatório] | O nome do serviço. |

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