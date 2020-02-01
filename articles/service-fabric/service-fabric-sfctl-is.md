---
title: CLI do Azure Service Fabric-sfctl é
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerenciar a infraestrutura do.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906089"
---
# <a name="sfctl-is"></a>sfctl is
Consultar e enviar comandos para o serviço de infraestrutura.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| command | Invoca um comando administrativo na instância de serviço de infraestrutura fornecida. |
| consulta | Invoca uma consulta somente leitura na instância de serviço de infraestrutura fornecida. |

## <a name="sfctl-is-command"></a>sfctl é um comando
Invoca um comando administrativo na instância de serviço de infraestrutura fornecida.

Para clusters que têm uma ou mais instâncias do serviço de infraestrutura configuradas, essa API fornece uma maneira de enviar comandos específicos da infraestrutura para uma determinada instância do serviço de infraestrutura. Os comandos disponíveis e seus formatos de resposta correspondentes variam de acordo com a infraestrutura em que o cluster está em execução. Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [Obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --service-id | A identidade do serviço de infraestrutura. <br><br> Este é o nome completo do serviço de infraestrutura sem o esquema de URI ' Fabric\:'. Esse parâmetro é necessário apenas para o cluster que tem mais de uma instância do serviço de infraestrutura em execução. |
| --Timeout-t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-is-query"></a>sfctl é consulta
Invoca uma consulta somente leitura na instância de serviço de infraestrutura fornecida.

Para clusters que têm uma ou mais instâncias do serviço de infraestrutura configuradas, essa API fornece uma maneira de enviar consultas específicas da infraestrutura para uma determinada instância do serviço de infraestrutura. Os comandos disponíveis e seus formatos de resposta correspondentes variam de acordo com a infraestrutura em que o cluster está em execução. Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [Obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --service-id | A identidade do serviço de infraestrutura. <br><br> Este é o nome completo do serviço de infraestrutura sem o esquema de URI ' Fabric\:'. Esse parâmetro é necessário apenas para o cluster que tem mais de uma instância do serviço de infraestrutura em execução. |
| --Timeout-t | Padrão\: 60. |

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
