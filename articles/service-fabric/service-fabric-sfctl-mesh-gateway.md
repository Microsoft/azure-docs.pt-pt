---
title: O Azure Service Fabric CLI - sfctl malha gateway | Documentos da Microsoft
description: Descreve os comandos de gateway de malha do CLI do Service Fabric sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 27cfe380e5988f17ca79c430cde7defc7f7bc440
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839084"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Obter e eliminar recursos de gateway de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o recurso de Gateway. |
| list | Apresenta uma lista de todos os recursos de gateway. |
| mostrar | Obtém o recurso de Gateway com o nome fornecido. |

## <a name="sfctl-mesh-gateway-delete"></a>o sfctl malha gateway delete
Elimina o recurso de Gateway.

Elimina o recurso de Gateway identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome -n [necessário] | O nome do recurso de gateway. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-gateway-list"></a>lista de gateways de malha sfctl
Apresenta uma lista de todos os recursos de gateway.

Obtém as informações sobre todos os recursos de gateway num grupo de recursos específico. As informações incluem a descrição e outras propriedades do Gateway.

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-gateway-show"></a>o sfctl malha gateway show
Obtém o recurso de Gateway com o nome fornecido.

Obtém as informações sobre o recurso de Gateway com o nome fornecido. As informações incluem a descrição e outras propriedades do Gateway.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome -n [necessário] | O nome do recurso de gateway. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).