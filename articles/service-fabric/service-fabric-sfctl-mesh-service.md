---
title: CLI do Azure Service Fabric-serviço de malha sfctl | Microsoft Docs
description: Descreve os comandos do serviço de malha do Service Fabric CLI sfctl.
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
ms.openlocfilehash: d2ddc34f8b298a4037a1c8286eceb9ef13b1f416
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901157"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Obter detalhes do serviço e listar serviços de um recurso de aplicativo.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| list | Lista todos os recursos de serviço. |
| mostrar | Obtém o recurso de serviço com o nome fornecido. |

## <a name="sfctl-mesh-service-list"></a>lista de serviços de malha sfctl
Lista todos os recursos de serviço.

Obtém as informações sobre todos os serviços de um recurso de aplicativo. As informações incluem a descrição e outras propriedades do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Name--nome-do-aplicativo [obrigatório] | O nome do aplicativo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-service-show"></a>exibição do serviço de malha sfctl
Obtém o recurso de serviço com o nome fornecido.

Obtém as informações sobre o recurso de serviço com o nome fornecido. As informações incluem a descrição e outras propriedades do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Name--nome-do-aplicativo [obrigatório] | O nome do aplicativo. |
| --Name-n [obrigatório] | O nome do serviço. |

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