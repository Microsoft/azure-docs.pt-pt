---
title: Propriedade CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para armazenar e consultar propriedades.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0a5ebd4822c5f0ff1735464bb4d5b42c436ee529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260318"
---
# <a name="sfctl-property"></a>sfctl property
Armazenar e consultar propriedades com nomes de Tecido de Serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina a propriedade de Tecido de Serviço especificado. |
| get | Obtém a propriedade de Tecido de Serviço especificado. |
| lista | Obtém informações sobre todas as propriedades do Service Fabric com um nome próprio. |
| colocar | Cria ou atualiza uma propriedade de Service Fabric. |

## <a name="sfctl-property-delete"></a>propriedade sfctl apagar
Elimina a propriedade de Tecido de Serviço especificado.

Elimina a propriedade de Tecido de Serviço especificado sob um nome próprio. Uma propriedade deve ser criada antes de poder ser eliminada.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome-id [Obrigatório] | O nome de Tecido de Serviço, sem o \: esquema URI de tecido. |
| --nome de propriedade [Obrigatório] | Especifica o nome da propriedade para obter. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-property-get"></a>propriedade sfctl obter
Obtém a propriedade de Tecido de Serviço especificado.

Obtém a propriedade de Tecido de Serviço especificado com um nome próprio. Isto irá sempre devolver valor e metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome-id [Obrigatório] | O nome de Tecido de Serviço, sem o \: esquema URI de tecido. |
| --nome de propriedade [Obrigatório] | Especifica o nome da propriedade para obter. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-property-list"></a>lista de propriedades sfctl
Obtém informações sobre todas as propriedades do Service Fabric com um nome próprio.

Um nome de Tecido de Serviço pode ter uma ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém a informação sobre estas propriedades numa lista de paged. A informação inclui nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome-id [Obrigatório] | O nome de Tecido de Serviço, sem o \: esquema URI de tecido. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --incluir valores | Permite especificar se deve incluir os valores dos imóveis devolvidos. Verdadeiro se os valores devem ser devolvidos com os metadados; Falso para devolver apenas metadados de propriedade. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-property-put"></a>propriedade sfctl colocado
Cria ou atualiza uma propriedade de Service Fabric.

Cria ou atualiza a propriedade de Tecido de Serviço especificado sob um nome próprio.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome-id [Obrigatório] | O nome de Tecido de Serviço, sem o \: esquema URI de tecido. |
| --nome de propriedade [Obrigatório] | O nome da propriedade Service Fabric. |
| --valor [Obrigatório] | Descreve um valor de propriedade de Service Fabric. Esta é uma corda JSON. <br><br> A cadeia json tem dois campos, o 'Kind' dos dados, e o valor, introduzido como 'Dados' dos dados. O valor 'Kind' deve ser o primeiro item a aparecer na corda JSON, e pode ser valores 'Binário', 'Int64', 'Double', 'String', ou 'Guid'. O valor deve ser serializativo para os tipos dados. Os valores 'Kind' e 'Data' devem ser fornecidos como cordas. |
| --tipo de id-personalizado | O iD do tipo personalizado da propriedade. Utilizando este imóvel, o utilizador é capaz de marcar o tipo de valor do imóvel. |
| --timeout -t | Padrão \: 60. |

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
