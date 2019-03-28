---
title: Atividade de Webhook no Azure Data Factory | Documentos da Microsoft
description: A atividade de Webhook não continue a execução do pipeline até que ele valida o conjunto de dados anexado com determinados critérios que especifica o utilizador.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 0b8b892f02e54c3b0ddb155af97ce63ff115bb1f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523002"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade de Webhook no Azure Data Factory
Pode utilizar uma atividade de hook de web para controlar a execução de pipelines através do seu código personalizado. Usando a atividade de webhook, os clientes podem chamar um ponto de extremidade e passar uma URL de retorno de chamada. A execução de pipeline aguarda o retorno de chamada a ser invocado antes de avançar para a atividade seguinte.

## <a name="syntax"></a>Sintaxe

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Propriedades do tipo



Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade de hook de web | String | Sim |
tipo | Tem de ser definido como **WebHook**. | String | Sim |
método | Método de REST API para o ponto de extremidade de destino. | cadeia de caracteres. Tipos suportados: "POST" | Sim |
url | Ponto de extremidade de destino e o caminho | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres). | Sim |
Cabeçalhos | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e o tipo de um pedido: "cabeçalhos": {"Accept-Language": "en-us", "Content-Type": "application/json"}. | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) | Sim, o cabeçalho Content-type é necessário. "headers":{ "Content-Type":"application/json"} |
corpo | Representa o payload que é enviado para o ponto final. | O corpo transmitido para a chamada de retorno do URI deve ser um JSON válido. Ver o esquema do payload de pedido no [esquema de payload de pedido](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) secção. | Sim |
autenticação | Método de autenticação utilizado para chamar o ponto final. Tipos suportados são "Básico" ou "ClientCertificate." Para obter mais informações, consulte [autenticação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) secção. Se a autenticação não é necessária, exclua esta propriedade. | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) | Não |
tempo limite | O tempo que a atividade de espera que o &#39;o callbackuri&#39; para ser invocada. O tempo que a atividade de espera que "o callbackuri' ser invocada. Valor predefinido é 10mins ("00: 10:00"). O formato é o período de tempo ou seja, d.hh:mm:ss | String | Não |

## <a name="additional-notes"></a>Notas adicionais

O Azure Data Factory irá transmitir uma propriedade adicional "o callbackuri" no corpo do ponto final do url e espera este uri para ser invocado antes do valor de tempo limite especificado. Se o uri não é chamado, a atividade falhará com o estado "Excedido".

A atividade de hook de web em si falhar apenas quando a chamada para o ponto final personalizado falha. Qualquer mensagem de erro pode ser adicionada no corpo do retorno de chamada e usada numa atividade subsequente.

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
