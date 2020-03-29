---
title: Azure Service Fabric CLI-sfctl property
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para armazenar e consultar propriedades.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905825"
---
# <a name="sfctl-property"></a>sfctl property
Armazenar e consultar propriedades sob nomes de Tecido de Serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina a propriedade especificada service fabric. |
| get | Obtém a propriedade especificada service fabric. |
| list | Obtém informações sobre todas as propriedades do Tecido de Serviço com um nome próprio. |
| colocar | Cria ou atualiza uma propriedade de Tecido de Serviço. |

## <a name="sfctl-property-delete"></a>propriedade sfctl excluir
Elimina a propriedade especificada service fabric.

Elimina a propriedade especificada service fabric com um nome próprio. Uma propriedade deve ser criada antes de ser eliminada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome Tecido de Serviço, sem o esquema URI 'tecido'.\: |
| --propriedade-nome [Obrigatório] | Especifica o nome da propriedade para obter. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-property-get"></a>propriedade sfctl obter
Obtém a propriedade especificada service fabric.

Obtém a propriedade especificada service fabric com um nome próprio. Isto irá sempre devolver valor e metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome Tecido de Serviço, sem o esquema URI 'tecido'.\: |
| --propriedade-nome [Obrigatório] | Especifica o nome da propriedade para obter. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-property-list"></a>lista de propriedade sfctl
Obtém informações sobre todas as propriedades do Tecido de Serviço com um nome próprio.

Um nome de Tecido de Serviço pode ter uma ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém a informação sobre estas propriedades numa lista de páginas. A informação inclui nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome Tecido de Serviço, sem o esquema URI 'tecido'.\: |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| --incluir valores | Permite especificar se inclui os valores das propriedades devolvidas. É verdade que os valores devem ser devolvidos com os metadados; Falso para devolver apenas metadados de propriedade. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-property-put"></a>propriedade sfctl colocar
Cria ou atualiza uma propriedade de Tecido de Serviço.

Cria ou atualiza a propriedade especificada service fabric com um nome próprio.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome Tecido de Serviço, sem o esquema URI 'tecido'.\: |
| --propriedade-nome [Obrigatório] | O nome da propriedade Service Fabric. |
| --valor [Obrigatório] | Descreve um valor de propriedade de Tecido de Serviço. Esta é uma corda JSON. <br><br> A cadeia json tem dois campos, o 'Kind' dos dados, e o valor, introduzido como 'Data' dos dados. O valor 'Kind' deve ser o primeiro item a aparecer na cadeia JSON, e pode ser os valores 'Binary', 'Int64', 'Double', 'String' ou 'Guid'. O valor deve ser serializável para os tipos. Tanto os valores "Kind" como os 'Data' devem ser fornecidos como cordas. |
| --personalizado-id-tipo | O id personalizado da propriedade. Utilizando esta propriedade, o utilizador é capaz de etiquetar o tipo do valor do imóvel. |
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