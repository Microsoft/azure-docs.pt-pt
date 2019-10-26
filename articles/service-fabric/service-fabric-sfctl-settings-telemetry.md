---
title: CLI do Azure Service Fabric-telemetria de configurações de sfctl | Microsoft Docs
description: Descreve os comandos de telemetria das configurações de sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: d926c71ae8fd9e196b86c14c7fb96cc65b587b65
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900984"
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
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
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


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).