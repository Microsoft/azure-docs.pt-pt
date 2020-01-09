---
title: CLI do Azure Service Fabric-Propriedade sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para propriedades de texto e consulta.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: d9129f07db9f3499b51707965d10426dbc3d3c12
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639110"
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
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --property-name [obrigatório] | Especifica o nome da propriedade a ser obtida. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-property-get"></a>Get da propriedade sfctl
Obtém a propriedade de Service Fabric especificada.

Obtém a propriedade Service Fabric especificada em um determinado nome. Isso sempre retornará o valor e os metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --property-name [obrigatório] | Especifica o nome da propriedade a ser obtida. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-property-list"></a>lista de propriedades sfctl
Obtém informações sobre todas as propriedades de Service Fabric em um determinado nome.

Um nome de Service Fabric pode ter uma ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém as informações sobre essas propriedades em uma lista paginável. As informações incluem nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --include-valores | Permite especificar se os valores das propriedades retornadas devem ser incluídos. True se os valores devem ser retornados com os metadados; False para retornar apenas os metadados de propriedade. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-property-put"></a>sfctl da propriedade put
Cria ou atualiza uma propriedade Service Fabric.

Cria ou atualiza a propriedade de Service Fabric especificada em um determinado nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema de URI ' Fabric\:'. |
| --property-name [obrigatório] | O nome da propriedade de Service Fabric. |
| --valor [obrigatório] | Descreve um valor de Propriedade Service Fabric. Esta é uma cadeia de caracteres JSON. <br><br> A cadeia de caracteres JSON tem dois campos, o ' tipo ' dos dados e o ' valor ' dos dados. O valor ' Kind ' deve ser o primeiro item a ser exibido na cadeia de caracteres JSON e pode ser os valores ' binary ', ' Int64 ', ' Double ', ' String ' ou ' GUID '. O valor deve ser serializado para os tipos especificados. Os valores ' Kind ' e ' data ' devem ser fornecidos como cadeias de caracteres. |
| --custom-id-type | A ID de tipo personalizado da propriedade. Usando essa propriedade, o usuário é capaz de marcar o tipo do valor da propriedade. |
| --Timeout-t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).