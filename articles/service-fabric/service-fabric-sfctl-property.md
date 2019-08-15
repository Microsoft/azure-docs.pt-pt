---
title: CLI do Azure Service Fabric-Propriedade sfctl | Microsoft Docs
description: Descreve os comandos de Propriedade do Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 34e6fc0d4e6e0817f9312a6565a2dd5dd99fdab9
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035256"
---
# <a name="sfctl-property"></a>sfctl property
Armazenar e consultar Propriedades em nomes de Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eliminar | Exclui a propriedade de Service Fabric especificada. |
| Get | Obtém a propriedade de Service Fabric especificada. |
| list | Obtém informações sobre todas as propriedades de Service Fabric em um determinado nome. |
| Posicione | Cria ou atualiza uma propriedade Service Fabric. |

## <a name="sfctl-property-delete"></a>exclusão da propriedade sfctl
Exclui a propriedade de Service Fabric especificada.

Exclui a propriedade Service Fabric especificada em um determinado nome. É necessário criar uma propriedade antes que ela possa ser excluída.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema\:de URI ' Fabric '. |
| --property-name [obrigatório] | Especifica o nome da propriedade a ser obtida. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-property-get"></a>Get da propriedade sfctl
Obtém a propriedade de Service Fabric especificada.

Obtém a propriedade Service Fabric especificada em um determinado nome. Isso sempre retornará o valor e os metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema\:de URI ' Fabric '. |
| --property-name [obrigatório] | Especifica o nome da propriedade a ser obtida. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-property-list"></a>lista de propriedades sfctl
Obtém informações sobre todas as propriedades de Service Fabric em um determinado nome.

Um nome de Service Fabric pode ter uma ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém as informações sobre essas propriedades em uma lista paginável. As informações incluem nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema\:de URI ' Fabric '. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --include-valores | Permite especificar se os valores das propriedades retornadas devem ser incluídos. True se os valores devem ser retornados com os metadados; False para retornar apenas os metadados de propriedade. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-property-put"></a>sfctl da propriedade put
Cria ou atualiza uma propriedade Service Fabric.

Cria ou atualiza a propriedade de Service Fabric especificada em um determinado nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Name-ID [obrigatório] | O nome do Service Fabric, sem o esquema\:de URI ' Fabric '. |
| --property-name [obrigatório] | O nome da propriedade de Service Fabric. |
| --valor [obrigatório] | Descreve um valor de Propriedade Service Fabric. Esta é uma cadeia de caracteres JSON. <br><br> A cadeia de caracteres JSON tem dois campos, o ' tipo ' dos dados e o ' valor ' dos dados. O valor ' Kind ' deve ser o primeiro item a ser exibido na cadeia de caracteres JSON e pode ser os valores ' binary ', ' Int64 ', ' Double ', ' String ' ou ' GUID '. O valor deve ser serializado para os tipos especificados. Os valores ' Kind ' e ' data ' devem ser fornecidos como cadeias de caracteres. |
| --custom-id-type | A ID de tipo personalizado da propriedade. Usando essa propriedade, o usuário é capaz de marcar o tipo do valor da propriedade. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).