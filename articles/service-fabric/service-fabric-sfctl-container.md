---
title: Tecido de serviço Azure CLI- recipiente sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para contentores.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906112"
---
# <a name="sfctl-container"></a>sfctl container
Executar comandos relacionados com contentores num nó de aglomerado.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| invocar-api | Invoque o recipiente API num recipiente implantado num nó de tecido de serviço para a embalagem de código dada. |
| registos | Coloca os troncos do recipiente para o recipiente implantados num nó de tecido de serviço. |

## <a name="sfctl-container-invoke-api"></a>sfctl recipiente invoca-api
Invoque o recipiente API num recipiente implantado num nó de tecido de serviço para a embalagem de código dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --code-package-instância-id [Obrigatório] | ID que identifica exclusivamente uma instância de pacote de código implantada num nó de tecido de serviço. <br><br> Pode ser recuperado pela "lista de pacotes de código de serviço". |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --contentor-api-uri-caminho [Obrigatório] | Caminho do Recipiente REST API URI, utilize '{ID}' no lugar do nome/id do recipiente. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --contentor-api-corpo | HTTP solicitar corpo para recipiente REST API. |
| --contentor-api-tipo | Tipo de conteúdo para recipiente REST API, predefinições de 'aplicação/json'. |
| --contentor-api-http-verbo | Verbo HTTP para recipiente REST API, incumprimentos para GET. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-container-logs"></a>troncos de contentor esfisso
Coloca os troncos do recipiente para o recipiente implantados num nó de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --code-package-instância-id [Obrigatório] | Id de instância de pacote de código, que pode ser recuperado por "lista de pacotes de código de serviço". |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --cauda | Número de linhas para mostrar a partir do fim dos registos. O padrão é 100. 'todos' para mostrar os registos completos. |
| --timeout -t | Padrão\: 60. |

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