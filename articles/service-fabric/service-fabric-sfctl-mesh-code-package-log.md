---
title: CLI do Azure Service Fabric-sfctl de malha código-pacote-log | Microsoft Docs
description: Descreve os comandos do Service Fabric interface de código de malha sfctl da CLI.
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
ms.openlocfilehash: b1949f87dcdb1e3d9fe8e7fd08d8d8ba3b8203a0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036435"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obtenha os logs do contêiner do pacote de códigos especificado para a réplica de serviço fornecida.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Get | Obtém os logs do contêiner. |

## <a name="sfctl-mesh-code-package-log-get"></a>código de malha sfctl-pacote-obter log
Obtém os logs do contêiner.

Obtém os logs do contêiner do pacote de códigos especificado da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Name--nome-do-aplicativo [obrigatório] | O nome do aplicativo. |
| --Code-Package-Name [obrigatório] | O nome do pacote de códigos do serviço. |
| --nome da réplica [obrigatório] | Nome da réplica de Service Fabric. |
| --Service-Name [obrigatório] | O nome do serviço. |
| --cauda | Número de linhas a serem mostradas do final dos logs. O padrão é 100. ' todos ' para mostrar os logs completos. |

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