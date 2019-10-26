---
title: CLI do Azure Service Fabric-serviço de malha sfctl-réplica | Microsoft Docs
description: Descreve os comandos de sfctl de malha de serviço da CLI do Service Fabric.
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
ms.openlocfilehash: e8eaa5fd7c9eeeff3f70a949b04d0fbf5f6c388f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901161"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obter detalhes da réplica e listar réplicas de um determinado serviço em um recurso de aplicativo.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| list | Lista todas as réplicas de um serviço. |
| mostrar | Obtém a réplica fornecida do serviço de um aplicativo. |

## <a name="sfctl-mesh-service-replica-list"></a>serviço de malha sfctl – lista de réplicas
Lista todas as réplicas de um serviço.

Obtém as informações sobre todas as réplicas de um serviço. As informações incluem a descrição e outras propriedades da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Name--nome-do-aplicativo [obrigatório] | O nome do aplicativo. |
| --Service-Name [obrigatório] | O nome do serviço. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-service-replica-show"></a>serviço de malha sfctl – mostrar réplica
Obtém a réplica fornecida do serviço de um aplicativo.

Obtém as informações sobre a réplica de serviço com o nome fornecido. As informações incluem a descrição e outras propriedades da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Name--nome-do-aplicativo [obrigatório] | O nome do aplicativo. |
| --Name-n [obrigatório] | O nome da réplica de serviço. |
| --Service-Name [obrigatório] | O nome do serviço. |

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