---
title: Implantação de malha CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para a criação de recursos de malha de tecido de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257280"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Criar recursos de malha de tecido de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implantação de Recursos de Malha de Tecido de Serviço. |

## <a name="sfctl-mesh-deployment-create"></a>implementação de malha sfctl criar
Cria uma implantação de Recursos de Malha de Tecido de Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --input-yaml-files [Obrigatório] | Percursos de ficheiros relativos ou absolutos separados em vírgula de todos os ficheiros yaml ou caminho relativo ou absoluto do diretório (recursivo) que contêm ficheiros yaml. |
| --parâmetros | Um caminho relativo ou absoluto para um ficheiro yaml ou um objeto json que contém os parâmetros que precisam ser ultrapassados. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

### <a name="examples"></a>Exemplos

Consolida e implanta todos os recursos para cluster, sobrepor os parâmetros mencionados no ficheiro yaml
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Consolida e implanta todos os recursos num diretório para cluster, sobrepor os parâmetros mencionados no ficheiro yaml

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implanta todos os recursos num diretório para clustering, sobrepondo-se aos parâmetros que são passados diretamente como objeto json
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
