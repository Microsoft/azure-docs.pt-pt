---
title: CLI do Azure Service Fabric-aplicativo de malha sfctl | Microsoft Docs
description: Descreve os comandos de aplicativo de malha sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 7e560b08290146b4a497539ecc180f8ae4431246
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035166"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Obter e excluir recursos do aplicativo.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eliminar | Exclui o recurso de aplicativo. |
| list | Lista todos os recursos do aplicativo. |
| mostrar | Obtém o recurso de aplicativo com o nome fornecido. |

## <a name="sfctl-mesh-app-delete"></a>exclusão do aplicativo de malha sfctl
Exclui o recurso de aplicativo.

Exclui o recurso de aplicativo identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-n [obrigatório] | O nome do aplicativo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-app-list"></a>lista de aplicativos de malha sfctl
Lista todos os recursos do aplicativo.

Obtém as informações sobre todos os recursos do aplicativo em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do aplicativo.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-app-show"></a>Mostrar aplicativo de malha sfctl
Obtém o recurso de aplicativo com o nome fornecido.

Obtém as informações sobre o recurso de aplicativo com o nome fornecido. As informações incluem a descrição e outras propriedades do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-n [obrigatório] | O nome do aplicativo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).