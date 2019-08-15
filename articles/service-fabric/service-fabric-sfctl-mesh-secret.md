---
title: CLI do Azure Service Fabric-segredo de malha sfctl | Microsoft Docs
description: Descreve os comandos secretos de malha do sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 5e58e6a401cc166c176dc465d58ba9e8a8ed83b0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035965"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Obter e excluir recursos de segredo de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eliminar | Exclui o recurso secreto. |
| list | Lista todos os recursos secretos. |
| mostrar | Obtém o recurso secreto com o nome fornecido. |

## <a name="sfctl-mesh-secret-delete"></a>exclusão do segredo de malha do sfctl
Exclui o recurso secreto.

Exclui o recurso secreto especificado e todos os seus valores nomeados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-n [obrigatório] | O nome do segredo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-secret-list"></a>lista de segredos da malha de sfctl
Lista todos os recursos secretos.

Obtém as informações sobre todos os recursos secretos em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do segredo.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-mesh-secret-show"></a>Mostrar segredo de malha sfctl
Obtém o recurso secreto com o nome fornecido.

Obtém as informações sobre o recurso secreto com o nome fornecido. As informações incluem a descrição e outras propriedades do segredo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-n [obrigatório] | O nome do segredo. |

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