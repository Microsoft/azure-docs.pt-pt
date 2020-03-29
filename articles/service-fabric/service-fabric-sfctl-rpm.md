---
title: Tecido de serviço Azure CLI-sfctl rpm
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para o serviço de reparação.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904940"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consulta e envie comandos para o serviço de reparação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| aprovar a força | Força a aprovação da tarefa de reparação. |
| delete | Elimina uma tarefa de reparação completa. |
| list | Obtém uma lista de tarefas de reparação que combinam com os filtros. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm aprovar força
Força a aprovação da tarefa de reparação.

Esta API suporta a plataforma Service Fabric; não se destina a ser utilizado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --task-id [Obrigatório] | A identificação da tarefa de reparação. |
| -versão | O número atual da versão atual da tarefa de reparação. Se não for zero, o pedido só será bem sucedido se este valor corresponder à versão atual real da tarefa de reparação. Se zero, então não é realizada verificação da versão. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm apagar
Elimina uma tarefa de reparação completa.

Esta API suporta a plataforma Service Fabric; não se destina a ser utilizado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --task-id [Obrigatório] | A identificação da tarefa de reparação completa a eliminar. |
| -versão | O número atual da versão atual da tarefa de reparação. Se não for zero, o pedido só será bem sucedido se este valor corresponder à versão atual real da tarefa de reparação. Se zero, então não é realizada verificação da versão. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-rpm-list"></a>sfctl rpm lista
Obtém uma lista de tarefas de reparação que combinam com os filtros.

Esta API suporta a plataforma Service Fabric; não se destina a ser utilizado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --filtro executor | O nome do executor de reparação cujas alegadas tarefas devem ser incluídas na lista. |
| --filtro do estado | Um bitwise-OR dos seguintes valores, especificando quais os estados de tarefa saem incluídos na lista de resultados. <ul><li>1 - Criado</li><li>2 - Reclamado</li><li>4 - Preparação</li><li>8 - Aprovado</li><li>16 - Execução</li><li>32 - Restauro</li><li>64 - Concluído</li></ul>
| --task-id-filter | O prefixo de id de tarefa de reparação a combinar. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
