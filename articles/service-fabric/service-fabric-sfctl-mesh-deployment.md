---
title: CLI do Azure Service Fabric-implantação de malha sfctl | Microsoft Docs
description: Descreve os comandos de implantação de malha do Service Fabric CLI sfctl.
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
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035185"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Crie Service Fabric recursos de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| create | Cria uma implantação de Service Fabric recursos de malha. |

## <a name="sfctl-mesh-deployment-create"></a>criação de implantação de malha sfctl
Cria uma implantação de Service Fabric recursos de malha.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Input-YAML-files [obrigatório] | Caminhos de arquivo relativo/absoluto separados por vírgula de todos os arquivos YAML ou caminho relativo/absoluto do diretório (recursivo) que contêm arquivos YAML. |
| --parâmetros | Um caminho relativo/absoluto para o arquivo YAML ou um objeto JSON que contém os parâmetros que precisam ser substituídos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Consolida e implanta todos os recursos no cluster substituindo os parâmetros mencionados no arquivo YAML

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros mencionados no arquivo YAML

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros, que são passados diretamente como objeto JSON

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).