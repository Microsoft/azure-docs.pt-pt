---
title: CLI do Azure Service Fabric-segredo de malha sfctl | Microsoft Docs
description: Descreve os comandos secretovalue da malha sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901184"
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