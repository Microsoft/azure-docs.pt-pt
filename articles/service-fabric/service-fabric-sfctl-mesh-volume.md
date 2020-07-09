---
title: Volume de malha CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para obter e apagar recursos de volume.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0e5ba1a311bd0480c56ee40edf01fc13ec01664a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905905"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Obter e apagar recursos de volume.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o recurso Volume. |
| list | Lista todos os recursos de volume. |
| Mostrar | Obtém o recurso Volume com o nome próprio. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl volume de malha eliminar
Elimina o recurso Volume.

Elimina o recurso Volume identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome -n [Obrigatório] | O nome do volume. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-volume-list"></a>lista de volume de malha sfctl
Lista todos os recursos de volume.

Obtém a informação sobre todos os recursos de volume num determinado grupo de recursos. As informações incluem a descrição e outras propriedades do Volume.

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-mesh-volume-show"></a>sfctl show de volume de malha
Obtém o recurso Volume com o nome próprio.

Obtém a informação sobre o recurso Volume com o nome próprio. As informações incluem a descrição e outras propriedades do Volume.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome -n [Obrigatório] | O nome do volume. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](/azure/service-fabric/scripts/sfctl-upgrade-application)da amostra .