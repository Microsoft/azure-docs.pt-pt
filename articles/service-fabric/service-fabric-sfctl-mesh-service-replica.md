---
title: Serviço de réplica de malha do Azure Service Fabric CLI - sfctl | Documentos da Microsoft
description: Descreve os comandos de réplica do serviço de malha do CLI do Service Fabric sfctl.
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
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038453"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtenha os detalhes de réplica e de réplicas de lista de um determinado serviço num recurso de aplicação.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| list | Apresenta uma lista de todas as réplicas de um serviço. |
| mostrar | Obtém a réplica especificada do serviço de um aplicativo. |

## <a name="sfctl-mesh-service-replica-list"></a>lista de réplicas do serviço de malha sfctl
Apresenta uma lista de todas as réplicas de um serviço.

Obtém as informações sobre todas as réplicas de um serviço. As informações incluem a descrição e outras propriedades da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação – nome da aplicação [necessário] | O nome da aplicação. |
| --nome do serviço [necessário] | O nome do serviço. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-mesh-service-replica-show"></a>show de réplica do serviço de malha sfctl
Obtém a réplica especificada do serviço de um aplicativo.

Obtém as informações sobre a réplica de serviço com o nome fornecido. As informações incluem a descrição e outras propriedades da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação – nome da aplicação [necessário] | O nome da aplicação. |
| -nome -n [necessário] | O nome da réplica de serviço. |
| --nome do serviço [necessário] | O nome do serviço. |

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