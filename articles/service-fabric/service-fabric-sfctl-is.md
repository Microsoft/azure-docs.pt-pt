---
title: CLI do Azure Service Fabric-sfctl é | Microsoft Docs
description: Descreve o Service Fabric CLI sfctl é comandos.
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
ms.openlocfilehash: 998a94350250402d4face64f64e12f32cf5b2a36
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036488"
---
# <a name="sfctl-is"></a>sfctl is
Consultar e enviar comandos para o serviço de infraestrutura.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Comando | Invoca um comando administrativo na instância de serviço de infraestrutura fornecida. |
| query | Invoca uma consulta somente leitura na instância de serviço de infraestrutura fornecida. |

## <a name="sfctl-is-command"></a>sfctl é um comando
Invoca um comando administrativo na instância de serviço de infraestrutura fornecida.

Para clusters que têm uma ou mais instâncias do serviço de infraestrutura configuradas, essa API fornece uma maneira de enviar comandos específicos da infraestrutura para uma determinada instância do serviço de infraestrutura. Os comandos disponíveis e seus formatos de resposta correspondentes variam de acordo com a infraestrutura em que o cluster está em execução. Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --service-id | A identidade do serviço de infraestrutura. <br><br> Este é o nome completo do serviço de infraestrutura sem o esquema de\:URI ' Fabric '. Esse parâmetro é necessário apenas para o cluster que tem mais de uma instância do serviço de infraestrutura em execução. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-is-query"></a>sfctl é consulta
Invoca uma consulta somente leitura na instância de serviço de infraestrutura fornecida.

Para clusters que têm uma ou mais instâncias do serviço de infraestrutura configuradas, essa API fornece uma maneira de enviar consultas específicas da infraestrutura para uma determinada instância do serviço de infraestrutura. Os comandos disponíveis e seus formatos de resposta correspondentes variam de acordo com a infraestrutura em que o cluster está em execução. Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --service-id | A identidade do serviço de infraestrutura. <br><br> Este é o nome completo do serviço de infraestrutura sem o esquema de\:URI ' Fabric '. Esse parâmetro é necessário apenas para o cluster que tem mais de uma instância do serviço de infraestrutura em execução. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).