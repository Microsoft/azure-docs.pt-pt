---
title: Tecido de serviço Azure CLI-sfctl malha código-pacote-log
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para a obtenção de registos para um pacote de código especificado.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906042"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obtenha os registos do recipiente do pacote de código especificado para a réplica do serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| get | Tira os troncos do contentor. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl malha código-pacote-log obter
Tira os troncos do contentor.

Obtenha os registos do recipiente do pacote de código especificado da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação --nome da aplicação [Obrigatório] | O nome da aplicação. |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código do serviço. |
| --réplica -nome [Obrigatório] | Nome de réplica de tecido de serviço. |
| --nome do serviço [Obrigatório] | O nome do serviço. |
| --cauda | Número de linhas para mostrar a partir do fim dos registos. O padrão é 100. 'todos' para mostrar os registos completos. |

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