---
title: Telemetria de configurações de tecido de serviço Azure CLI-sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para configurar a telemetria sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76903023"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Configure as definições de telemetria locais para este caso de sfctl.

A telemetria Sfctl recolhe o nome de comando sem parâmetros fornecidos ou os seus valores, versão sfctl, tipo OS, versão python, o sucesso ou falha do comando, a mensagem de erro devolvida.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| set-telemetria | Ligar ou desligar a telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl configurações telemetria set-telemetria
Ligar ou desligar a telemetria.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -off | Desligue a telemetria. |
| --on | Liga a telemetria. Este é o valor predefinido. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

### <a name="examples"></a>Exemplos

Desligue a telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Liga a telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).