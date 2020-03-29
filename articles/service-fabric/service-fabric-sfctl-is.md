---
title: Tecido de serviço Azure CLI-sfctl é
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerir infraestruturas.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906089"
---
# <a name="sfctl-is"></a>sfctl is
Consulta e envie comandos para o serviço de infraestrutura.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| command | Invoca um comando administrativo sobre a instância do Serviço de Infraestruturas. |
| consulta | Invoca uma consulta apenas para leitura sobre a instância do serviço de infraestruturas. |

## <a name="sfctl-is-command"></a>sfctl é comando
Invoca um comando administrativo sobre a instância do Serviço de Infraestruturas.

Para clusters que tenham um ou mais casos do Serviço de Infraestruturas configurados, esta API fornece uma forma de enviar comandos específicos de infraestruturas para uma determinada instância do Serviço de Infraestruturas. Os comandos disponíveis e os respetivos formatos de resposta variam consoante a infraestrutura em que o cluster está a funcionar. Esta API suporta a plataforma Service Fabric; não se destina a ser utilizado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [Obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --serviço-id | A identidade do serviço de infraestruturas. <br><br> Este é o nome completo do serviço\:de infraestruturas sem o regime URI de tecido. Este parâmetro necessário apenas para o cluster que tem mais de um exemplo de serviço de infraestrutura em funcionamento. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-is-query"></a>sfctl é consulta
Invoca uma consulta apenas para leitura sobre a instância do serviço de infraestruturas.

Para clusters que tenham um ou mais casos do Serviço de Infraestruturas configurados, esta API fornece uma forma de enviar consultas específicas de infraestruturas para um determinado exemplo do Serviço de Infraestruturas. Os comandos disponíveis e os respetivos formatos de resposta variam consoante a infraestrutura em que o cluster está a funcionar. Esta API suporta a plataforma Service Fabric; não se destina a ser utilizado diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [Obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --serviço-id | A identidade do serviço de infraestruturas. <br><br> Este é o nome completo do serviço\:de infraestruturas sem o regime URI de tecido. Este parâmetro necessário apenas para o cluster que tem mais de um exemplo de serviço de infraestrutura em funcionamento. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
