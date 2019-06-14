---
title: Segredo do malha do Azure Service Fabric CLI - sfctl | Documentos da Microsoft
description: Descreve os comandos do CLI do Service Fabric sfctl malha secretos.
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
ms.openlocfilehash: acc5862a8794da4fde991025eacfcccf6e16916a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607693"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Obter e eliminar recursos secreta da malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o recurso secreto. |
| list | Apresenta uma lista de todos os recursos secretos. |
| mostrar | Obtém o recurso secreto com o nome fornecido. |

## <a name="sfctl-mesh-secret-delete"></a>eliminação de segredo de malha sfctl
Elimina o recurso secreto.

Elimina o recurso de segredo especificado e todos os seus valores com nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome -n [necessário] | O nome do segredo do. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-secret-list"></a>lista de segredo de malha sfctl
Apresenta uma lista de todos os recursos secretos.

Obtém as informações sobre todos os recursos secretas num grupo de recursos específico. As informações incluem a descrição e outras propriedades do segredo do.

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-secret-show"></a>show de segredo de malha sfctl
Obtém o recurso secreto com o nome fornecido.

Obtém as informações sobre o recurso secreta com o nome fornecido. As informações incluem a descrição e outras propriedades do segredo do.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome -n [necessário] | O nome do segredo do. |

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