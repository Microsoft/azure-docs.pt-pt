---
title: Azure Service Fabric CLI sfctl malha implantação | Documentos da Microsoft
description: Descreve os comandos de implementação de malha do CLI do Service Fabric sfctl.
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
ms.openlocfilehash: e6b484dabd77a142961db2d97242896790fa3d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836965"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Crie recursos de malha do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implementação de recursos de malha do Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>criar a implementação de malha sfctl
Cria uma implementação de recursos de malha do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --yaml-os ficheiros de entrada [necessários] | Caminhos de ficheiro de caminho relativo/absoluto separados por vírgulas de todos os ficheiros de yaml ou absoluto/relativo do caminho do diretório (recursiva) que contêm ficheiros yaml. |
| -parâmetros | Um caminho relativo/absoluto para o ficheiro yaml ou um objeto json que contém os parâmetros que têm de ser substituído. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

### <a name="examples"></a>Exemplos

Consolida e implementa todos os recursos em cluster ao substituir os parâmetros mencionados no ficheiro yaml.

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Consolida e implementa todos os recursos num diretório cluster ao substituir os parâmetros mencionados no ficheiro yaml.

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implementa todos os recursos num diretório de cluster através da substituição de parâmetros, que são transmitidos diretamente como objeto json

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Passos Seguintes
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).