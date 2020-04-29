---
title: Tecido de serviço Azure CLI-segredo de malha sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para obter e apagar recursos secretos de Malha de Tecido de Serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905978"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Obtenha e elimine os recursos secretos da malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o recurso Secreto. |
| list | Lista todos os recursos secretos. |
| Mostrar | Obtém o recurso secreto com o nome dado. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl segredo malha excluir
Elimina o recurso Secreto.

Elimina o recurso Secreto especificado e todos os seus valores nomeados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome -n [Obrigatório] | O nome do recurso secreto. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-mesh-secret-list"></a>lista secreta de malha sfctl
Lista todos os recursos secretos.

Obtém a informação sobre todos os recursos secretos de um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do Segredo.

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-mesh-secret-show"></a>sfctl malha segredo show
Obtém o recurso secreto com o nome dado.

Obtém a informação sobre o recurso Secreto com o nome dado. As informações incluem a descrição e outras propriedades do Segredo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome -n [Obrigatório] | O nome do recurso secreto. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).