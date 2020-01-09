---
title: CLI do Azure Service Fabric-pacote de código de malha sfctl-log
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter logs para um pacote de códigos especificado.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 78edc9bb36b711f72300942bc9900b0fde7c51d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646148"
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
| --App-Name--nome-do-aplicativo [obrigatório] | O nome da aplicação. |
| --Code-Package-Name [obrigatório] | O nome do pacote de códigos do serviço. |
| --nome da réplica [obrigatório] | Nome da réplica de Service Fabric. |
| --Service-Name [obrigatório] | O nome do serviço. |
| --cauda | Número de linhas a serem mostradas do final dos logs. O padrão é 100. ' todos ' para mostrar os logs completos. |

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