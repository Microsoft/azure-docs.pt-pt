---
title: Azure Service Fabric CLI-sfctl define telemetria
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para configurar telemetria sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245517"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Configurar definições de telemetria locais a este caso de sfctl.

A telemetria Sfctl recolhe o nome de comando sem parâmetros fornecidos ou os seus valores, versão sfctl, tipo DE, versão python, o sucesso ou falha do comando, a mensagem de erro devolvida.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| set-telemetria | Ligue ou desligue a telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl definições telemetria set-telemetria
Ligue ou desligue a telemetria.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --off | Desligue a telemetria. |
| --on | Ligue a telemetria. Este é o valor predefinido. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

### <a name="examples"></a>Exemplos

Desligue a telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Ligue a telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
