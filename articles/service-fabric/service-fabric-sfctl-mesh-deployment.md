---
title: CLI do Azure Service Fabric-implantação de malha sfctl | Microsoft Docs
description: Descreve os comandos de implantação de malha do Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901257"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Crie Service Fabric recursos de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implantação de Service Fabric recursos de malha. |

## <a name="sfctl-mesh-deployment-create"></a>criação de implantação de malha sfctl
Cria uma implantação de Service Fabric recursos de malha.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Input-YAML-files [obrigatório] | Caminhos de arquivo relativos/absolutos separados por vírgula de todos os arquivos YAML ou caminho relativo/absoluto do diretório (recursivo) que contêm arquivos YAML. |
| --parâmetros | Um caminho relativo/absoluto para o arquivo YAML ou um objeto JSON que contém os parâmetros que precisam ser substituídos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
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

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros que são passados diretamente como objeto JSON
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).