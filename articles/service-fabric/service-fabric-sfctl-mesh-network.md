---
title: Rede de malha CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para obter e eliminar recursos de rede de malha de tecido de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245865"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Obter e apagar recursos de rede de malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o recurso Rede. |
| lista | Lista todos os recursos da rede. |
| Mostrar | Obtém o recurso rede com o nome próprio. |

## <a name="sfctl-mesh-network-delete"></a>rede de malha sfctl eliminar
Elimina o recurso Rede.

Elimina o recurso rede identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome -n [Obrigatório] | O nome da rede. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-network-list"></a>lista de rede de malha sfctl
Lista todos os recursos da rede.

Obtém a informação sobre todos os recursos da rede num determinado grupo de recursos. As informações incluem a descrição e outras propriedades da Rede.

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-network-show"></a>sfctl rede de malha show
Obtém o recurso rede com o nome próprio.

Obtém a informação sobre o recurso Network com o nome próprio. As informações incluem a descrição e outras propriedades da Rede.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome -n [Obrigatório] | O nome da rede. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
