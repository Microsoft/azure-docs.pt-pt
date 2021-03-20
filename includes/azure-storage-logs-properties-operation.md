---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011188"
---
| Propriedade | Descrição |
|:--- |:---|
|**Hora** | O tempo universal coordenado (UTC) quando o pedido foi recebido por armazenamento. Por exemplo: `2018/11/08 21:09:36.6900118`.|
|**recursosId** | A identificação de recursos da conta de armazenamento. Por exemplo: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**categoria** | A categoria da operação solicitada. Por exemplo: `StorageRead` `StorageWrite` , ou . `StorageDelete` .|
|**operationName** | O tipo de operação REST que foi realizada. <br> Para obter uma lista completa de operações, consulte [o tópico de Operações Registadas e Mensagens de Estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)do Storage Analytics . |
|**operaçãoVer** | A versão do serviço de armazenamento que foi especificada quando o pedido foi feito. Isto equivale ao valor do cabeçalho **versão x-ms.** Por exemplo: `2017-04-17`.|
|**schemaVersão** | A versão do esquema do registo. Por exemplo: `1.0`.|
|**statusCode** | O código de estado HTTP para o pedido. Se o pedido for interrompido, este valor poderá ser definido para `Unknown` . <br> Por exemplo: `206` |
|**statusTexto** | O estado da operação solicitada.  Para obter uma lista completa de mensagens de estado, consulte [o tópico de Operações registadas e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)do Storage Analytics . Na versão 2017-04-17 e mais tarde, a mensagem de estado `ClientOtherError` não é usada. Em vez disso, este campo contém um código de erro. Por exemplo: `SASSuccess`  |
|**duraçõesMs** | O tempo total, expresso em milissegundos, para a realização da operação solicitada. Isto inclui o tempo para ler o pedido de entrada, e para enviar a resposta ao solicitador. Por exemplo: `12`.|
|**callerIpAddress** | O endereço IP do solicitador, incluindo o número da porta. Por exemplo: `192.100.0.102:4362`. |
|**correlationId** | O ID que é usado para correlacionar registos entre os recursos. Por exemplo: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**localização** | A localização da conta de armazenamento. Por exemplo: `North Europe`. |
|**protocolo**|O protocolo que é usado na operação. Por exemplo: `HTTP` `HTTPS` , , `SMB` ou `NFS`|
| **uri** | Identificador de recursos uniformes que é solicitado. |