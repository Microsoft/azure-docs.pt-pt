---
title: Implantação de malha de malha de malha de malha azure service CLI- sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para a criação de recursos de malha de tecido de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906039"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Criar recursos de malha de tecido de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implantação de Recursos de Malha de Tecido de Serviço. |

## <a name="sfctl-mesh-deployment-create"></a>implantação de malha sfctl criar
Cria uma implantação de Recursos de Malha de Tecido de Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --input-yaml-files [Obrigatório] | Caminhos relativos ou absolutos separados da vírvia separada da vírvia de todos os ficheiros de yaml ou caminho relativo ou absoluto do diretório (recursivo) que contêm ficheiros de yaml. |
| --parâmetros | Um caminho relativo ou absoluto para um ficheiro de yaml ou um objeto json que contém os parâmetros que precisam ser ultrapassados. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

### <a name="examples"></a>Exemplos

Consolida e implanta todos os recursos para cluster, sobrepondo-se aos parâmetros mencionados no ficheiro yaml
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Consolida e implanta todos os recursos num diretório para cluster, ultrapassando os parâmetros mencionados no ficheiro yaml

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implanta todos os recursos num diretório para cluster, ultrapassando os parâmetros que são transmitidos diretamente como objeto json
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
