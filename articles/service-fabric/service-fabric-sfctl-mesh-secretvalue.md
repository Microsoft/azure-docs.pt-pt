---
title: O Azure Service Fabric CLI - sfctl malha secretvalue | Documentos da Microsoft
description: Descreve os comandos de secretvalue de malha do CLI do Service Fabric sfctl.
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
ms.openlocfilehash: 3f8e46f063d3e725e2174fd907169f3e0167586a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836955"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Obter e eliminar recursos de secretvalue da malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o valor especificado do recurso secreto com nome. |
| list | Nomes da lista de todos os valores do recurso secreto especificado. |
| mostrar | Obter o valor de uma versão especificada de um recurso secreta. |

## <a name="sfctl-mesh-secretvalue-delete"></a>o sfctl malha secretvalue delete
Elimina o valor especificado do recurso secreto com nome.

Elimina o recurso de valor secreto identificado pelo nome. O nome do recurso é, normalmente, a versão associada esse valor. A eliminação irá falhar se o valor especificado está em utilização.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome do segredo - n [necessário] | O nome do recurso secreto. |
| – versão - v [necessário] | O nome da versão secreta. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-secretvalue-list"></a>o sfctl malha secretvalue lista
Nomes da lista de todos os valores do recurso secreto especificado.

Obtém informações sobre todos os recursos de valor secreto do recurso secreta especificado. As informações incluem os nomes dos recursos de valor secreto, mas não os valores reais.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome do segredo - n [necessário] | O nome do recurso secreto. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-secretvalue-show"></a>o sfctl malha secretvalue show
Obter o valor de uma versão especificada de um recurso secreta.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome do segredo - n [necessário] | O nome do recurso secreto. |
| – versão - v [necessário] | O nome da versão secreta. |
| --show-value | Mostre o valor real da versão secreta. |

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