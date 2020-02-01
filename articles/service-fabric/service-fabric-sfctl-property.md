---
title: CLI do Azure Service Fabric-Propriedade sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para armazenar e consultar propriedades.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905825"
---
# <a name="sfctl-property"></a>sfctl property
Armazenar e consultar Propriedades em nomes de Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Exclui a propriedade de Service Fabric especificada. |
| Get | Obtém a propriedade de Service Fabric especificada. |
| list | Obtém informações sobre todas as propriedades de Service Fabric em um determinado nome. |
| Posicione | Cria ou atualiza uma propriedade Service Fabric. |

## <a name="sfctl-property-delete"></a>exclusão da propriedade sfctl
Exclui a propriedade de Service Fabric especificada.

Exclui a propriedade Service Fabric especificada em um determinado nome. É necessário criar uma propriedade antes que ela possa ser excluída.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --propriedade-nome [Obrigatório] | Especifica o nome da propriedade a ser obtida. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-property-get"></a>Get da propriedade sfctl
Obtém a propriedade de Service Fabric especificada.

Obtém a propriedade Service Fabric especificada em um determinado nome. Isso sempre retornará o valor e os metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --propriedade-nome [Obrigatório] | Especifica o nome da propriedade a ser obtida. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-property-list"></a>lista de propriedades sfctl
Obtém informações sobre todas as propriedades de Service Fabric em um determinado nome.

Um nome de Service Fabric pode ter uma ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém as informações sobre essas propriedades em uma lista paginável. As informações incluem nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --include-valores | Permite especificar se os valores das propriedades retornadas devem ser incluídos. True se os valores devem ser retornados com os metadados; False para retornar apenas os metadados de propriedade. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-property-put"></a>sfctl da propriedade put
Cria ou atualiza uma propriedade Service Fabric.

Cria ou atualiza a propriedade de Service Fabric especificada em um determinado nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome-id [Obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --propriedade-nome [Obrigatório] | O nome da propriedade de Service Fabric. |
| --valor [Obrigatório] | Descreve um valor de Propriedade Service Fabric. Esta é uma cadeia de caracteres JSON. <br><br> A cadeia json tem dois campos, o 'Kind' dos dados, e o valor, introduzido como 'Data' dos dados. O valor ' Kind ' deve ser o primeiro item a ser exibido na cadeia de caracteres JSON e pode ser os valores ' binary ', ' Int64 ', ' Double ', ' String ' ou ' GUID '. O valor deve ser serializado para os tipos especificados. Os valores ' Kind ' e ' data ' devem ser fornecidos como cadeias de caracteres. |
| --custom-id-type | A ID de tipo personalizado da propriedade. Usando essa propriedade, o usuário é capaz de marcar o tipo do valor da propriedade. |
| --timeout -t | Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Learn how to use the Service Fabric CLI using the [sample scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).