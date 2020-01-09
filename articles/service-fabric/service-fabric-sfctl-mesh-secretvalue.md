---
title: CLI do Azure Service Fabric-segredo de malha sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter e excluir Service Fabric recursos de segredo de malha.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: a29d32dff9ad51942acb30dd834ad6fbd362ac65
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646114"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Obter e excluir recursos de segredo de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Exclui o valor especificado do recurso secreto nomeado. |
| list | Listar nomes de todos os valores do recurso secreto especificado. |
| mostrar | Lista o valor especificado do recurso secreto. |

## <a name="sfctl-mesh-secretvalue-delete"></a>exclusão de segredo de malha de sfctl
Exclui o valor especificado do recurso secreto nomeado.

Exclui o recurso de valor secreto identificado pelo nome. O nome do recurso normalmente é a versão associada a esse valor. A exclusão falhará se o valor especificado estiver em uso.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Secret-Name-n [obrigatório] | O nome do recurso secreto. |
| --Version-v [obrigatório] | O nome da versão secreta. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-secretvalue-list"></a>lista de segredo de malha sfctl
Listar nomes de todos os valores do recurso secreto especificado.

Obtém informações sobre todos os recursos de valor secreto do recurso secreto especificado. As informações incluem os nomes dos recursos de valor secreto, mas não os valores reais.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Secret-Name-n [obrigatório] | O nome do recurso secreto. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl de malha do segredo mostrar
Lista o valor especificado do recurso secreto.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Secret-Name-n [obrigatório] | O nome do recurso secreto. |
| --Version-v [obrigatório] | O nome da versão secreta. |
| --Mostrar valor | Mostre o valor real da versão secreta. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).