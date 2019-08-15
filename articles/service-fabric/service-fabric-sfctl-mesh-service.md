---
title: CLI do Azure Service Fabric-serviço de malha sfctl | Microsoft Docs
description: Descreve os comandos do serviço de malha do Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 6afcb891de763f156705bc9825ab7575a272c1db
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035024"
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
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
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
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).