---
title: Azure Service Fabric CLI-sfctl é
description: Saiba mais sobre o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para a gestão de infraestruturas.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 349f70c32ea4ebb4559f053d5ef05b4b37b6480f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260374"
---
# <a name="sfctl-is"></a>sfctl is
Consultar e enviar comandos para o serviço de infraestrutura.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| command | Invoca um comando administrativo sobre a instância do Serviço de Infraestruturas. |
| consulta | Invoca uma consulta apenas de leitura sobre a dada instância de serviço de infraestrutura. |

## <a name="sfctl-is-command"></a>sfctl é comando
Invoca um comando administrativo sobre a instância do Serviço de Infraestruturas.

Para os clusters que têm um ou mais casos do Serviço de Infraestruturas configurados, esta API fornece uma forma de enviar comandos específicos da infraestrutura para uma instância específica do Serviço de Infraestruturas. Os comandos disponíveis e os seus formatos de resposta correspondentes variam consoante a infraestrutura em que o cluster está em execução. Esta API suporta a plataforma Service Fabric; não se destina a ser usado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --comando [Obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --serviço-id | A identidade do serviço de infraestrutura. <br><br> Este é o nome completo do serviço de infraestruturas sem o \: regime URI do tecido. Este parâmetro requer apenas para o cluster que tem mais de um caso de serviço de infraestrutura em funcionamento. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-is-query"></a>sfctl é consulta
Invoca uma consulta apenas de leitura sobre a dada instância de serviço de infraestrutura.

Para os agrupamentos que têm um ou mais casos do Serviço de Infraestruturas configurados, esta API fornece uma forma de enviar consultas específicas de infraestrutura para uma instância específica do Serviço de Infraestruturas. Os comandos disponíveis e os seus formatos de resposta correspondentes variam consoante a infraestrutura em que o cluster está em execução. Esta API suporta a plataforma Service Fabric; não se destina a ser usado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --comando [Obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --serviço-id | A identidade do serviço de infraestrutura. <br><br> Este é o nome completo do serviço de infraestruturas sem o \: regime URI do tecido. Este parâmetro requer apenas para o cluster que tem mais de um caso de serviço de infraestrutura em funcionamento. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
