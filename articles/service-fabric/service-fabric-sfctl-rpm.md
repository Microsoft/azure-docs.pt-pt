---
title: Azure Service Fabric CLI-sfctl rpm
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para o serviço de gerente de reparação.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7317fd66303aaabf5232106aa7391439880bebaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260294"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço de gerente de reparação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| aprovar força | Força a aprovação da tarefa de reparação dada. |
| delete | Elimina uma tarefa de reparação completa. |
| lista | Obtém uma lista de tarefas de reparação correspondentes aos filtros dados. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm aprovar força
Força a aprovação da tarefa de reparação dada.

Esta API suporta a plataforma Service Fabric; não se destina a ser usado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --task-id [Obrigatório] | A identificação da tarefa de reparação. |
| -versão | O número atual da versão da tarefa de reparação. Se não for zero, o pedido só será bem sucedido se este valor corresponder à versão atual da tarefa de reparação. Se zero, então não é feita nenhuma verificação de versão. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm apagar
Elimina uma tarefa de reparação completa.

Esta API suporta a plataforma Service Fabric; não se destina a ser usado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --task-id [Obrigatório] | A identificação da tarefa de reparação completa a eliminar. |
| -versão | O número atual da versão da tarefa de reparação. Se não for zero, o pedido só será bem sucedido se este valor corresponder à versão atual da tarefa de reparação. Se zero, então não é feita nenhuma verificação de versão. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-rpm-list"></a>sfctl rpm lista
Obtém uma lista de tarefas de reparação correspondentes aos filtros dados.

Esta API suporta a plataforma Service Fabric; não se destina a ser usado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --executor-filtro | O nome do executor de reparações cujas tarefas reclamadas devem ser incluídas na lista. |
| --estado-filtro | Um pouco de OR dos seguintes valores, especificando quais estados de tarefa devem ser incluídos na lista de resultados. <ul><li>1 - Criado</li><li>2 - Reclamado</li><li>4 - Preparação</li><li>8 - Aprovado</li><li>16 - Execução</li><li>32 - Restaurar</li><li>64 - Concluído</li></ul>
| --filtro de id-id de tarefa | O prefixo de identificação da tarefa de reparação a ser igualado. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI do Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
