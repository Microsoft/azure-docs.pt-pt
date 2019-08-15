---
title: CLI do Azure Service Fabric – rede de malha sfctl | Microsoft Docs
description: Descreve os comandos de rede de sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: e92fbc3856bba69db11aba6ad5d85e814bf30a81
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035052"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Obter e excluir recursos de rede de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eliminar | Exclui o recurso de rede. |
| list | Lista todos os recursos de rede. |
| mostrar | Obtém o recurso de rede com o nome fornecido. |

## <a name="sfctl-mesh-network-delete"></a>exclusão de rede de malha sfctl
Exclui o recurso de rede.

Exclui o recurso de rede identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-n [obrigatório] | O nome da rede. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-network-list"></a>lista de redes de malha de sfctl
Lista todos os recursos de rede.

Obtém as informações sobre todos os recursos de rede em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades da rede.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-network-show"></a>exibição de rede de malha sfctl
Obtém o recurso de rede com o nome fornecido.

Obtém as informações sobre o recurso de rede com o nome fornecido. As informações incluem a descrição e outras propriedades da rede.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-n [obrigatório] | O nome da rede. |

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