---
title: O Azure Service Fabric CLI sfctl propriedade | Documentos da Microsoft
description: Descreve os comandos de propriedade de sfctl de CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556600"
---
# <a name="sfctl-property"></a>sfctl property
Propriedades de consulta e Store em nomes do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina a propriedade especificada do Service Fabric. |
| Get | Obtém a propriedade especificada do Service Fabric. |
| list | Obtém informações sobre todas as propriedades do Service Fabric num determinado nome. |
| colocar | Cria ou atualiza uma propriedade de Service Fabric. |

## <a name="sfctl-property-delete"></a>o sfctl propriedade delete
Elimina a propriedade especificada do Service Fabric.

Elimina a propriedade especificada do Service Fabric num determinado nome. Uma propriedade tem de ser criada antes de poder ser eliminado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome-id [necessário] | Nome de recursos de infraestrutura do serviço, sem o "recursos de infraestrutura\:" esquema de URI. |
| --nome da propriedade [necessário] | Especifica o nome da propriedade a obter. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-property-get"></a>o sfctl propriedade get
Obtém a propriedade especificada do Service Fabric.

Obtém a propriedade especificada do Service Fabric num determinado nome. Isto irá sempre devolver o valor e metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome-id [necessário] | Nome de recursos de infraestrutura do serviço, sem o "recursos de infraestrutura\:" esquema de URI. |
| --nome da propriedade [necessário] | Especifica o nome da propriedade a obter. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-property-list"></a>lista de propriedades de sfctl
Obtém informações sobre todas as propriedades do Service Fabric num determinado nome.

Um nome de Service Fabric pode ter uma ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém as informações sobre estas propriedades numa lista paginada. As informações incluem o nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome-id [necessário] | Nome de recursos de infraestrutura do serviço, sem o "recursos de infraestrutura\:" esquema de URI. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| – valores incluem | Permite especificar se pretende incluir os valores de propriedades retornados. VERDADEIRO se os valores serão retornados com os metadados; FALSO para devolver apenas os metadados de propriedade. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-property-put"></a>o sfctl propriedade put
Cria ou atualiza uma propriedade de Service Fabric.

Cria ou atualiza a propriedade especificada do Service Fabric num determinado nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome-id [necessário] | Nome de recursos de infraestrutura do serviço, sem o "recursos de infraestrutura\:" esquema de URI. |
| --nome da propriedade [necessário] | O nome da propriedade do Service Fabric. |
| – o valor [necessário] | Descreve um valor de propriedade do Service Fabric. Esta é uma cadeia de caracteres do JSON. <br><br> A cadeia de caracteres do json tem dois campos, o "Kind" dos dados e o "valor" dos dados. O valor de "Kind" tem de ser o primeiro item apareça na cadeia de JSON e pode ser valores de "Binário", 'Int64', 'Double', 'String' ou 'Guid'. O valor deve conseguir serializar-os tipos determinados. Valores de "Kind" e "Dados" devem ser fornecidos como cadeias de caracteres. |
| --custom-id-type | Id do tipo personalizado da propriedade. Utilizar esta propriedade, o utilizador é capaz de identificar o tipo do valor da propriedade. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).