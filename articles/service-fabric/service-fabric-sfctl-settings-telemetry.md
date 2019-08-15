---
title: CLI do Azure Service Fabric-telemetria de configurações de sfctl | Microsoft Docs
description: Descreve os comandos de telemetria das configurações de sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035196"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Defina as configurações de telemetria locais para esta instância do sfctl.

A telemetria do Sfctl coleta o nome do comando sem parâmetros fornecidos ou seus valores, versão do Sfctl, tipo de so, versão do Python, êxito ou falha do comando, a mensagem de erro retornada.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| conjunto de telemetria | Ativar ou desativar a telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>conjunto de telemetria de configurações de sfctl – telemetria
Ativar ou desativar a telemetria.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --desativado | Desative a telemetria. |
| --em | Ative a telemetria. Este é o valor predefinido. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Desative a telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Ative a telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).