---
title: Recipiente CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para contentores.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245896"
---
# <a name="sfctl-container"></a>sfctl container
Executar comandos relacionados com contentores num nó de cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| invocar-api | Invocar a API do recipiente num recipiente implantado num nó de tecido de serviço para o pacote de código dado. |
| registos | Obtém os registos dos contentores para o contentor colocados num nó de tecido de serviço. |

## <a name="sfctl-container-invoke-api"></a>recipiente sfctl invocar-api
Invocar a API do recipiente num recipiente implantado num nó de tecido de serviço para o pacote de código dado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --código-pacote-id-id [Obrigatório] | ID que identifica exclusivamente uma instância de pacote de código implantada num nó de tecido de serviço. <br><br> Pode ser recuperado por "lista de pacotes de serviço". |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --container-api-uri-caminho [Obrigatório] | Percurso API URI do contentor REST, utilize '{ID}' no lugar do nome do recipiente/id. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-manifesto-nome [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --contentor-api-corpo | HTTP Solicitar o corpo para o contentor REST API. |
| --contentor-api-tipo de conteúdo | Tipo de conteúdo para a API de repouso do recipiente, predefinido a "aplicação/json". |
| --contentor-api-http-verbo | VERbo HTTP para API DE REPOUSO DO RECIPIENTE, predefinições para GET. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-container-logs"></a>sfctl troncos de contentores
Obtém os registos dos contentores para o contentor colocados num nó de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --código-pacote-id-id [Obrigatório] | ID de identificação de pacote de código, que pode ser recuperado por "lista de pacotes de serviço". |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-manifesto-nome [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --cauda | Número de linhas para mostrar a partir do final dos registos. O padrão é 100. 'todos' para mostrar os registos completos. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
