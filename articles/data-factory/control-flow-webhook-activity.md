---
title: Atividade de webhook no Azure Data Factory
description: A atividade de webhook não continua a execução do pipeline até que ele valide o conjunto de dado anexado com determinados critérios que o usuário especifica.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 3734472d9026d4e355c08b36d5ba58974288daac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678233"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade de webhook no Azure Data Factory
Você pode usar uma atividade de Web Hook para controlar a execução de pipelines por meio de seu código personalizado. Usando a atividade de webhook, os clientes podem chamar um ponto de extremidade e passar uma URL de retorno de chamada. A execução do pipeline aguarda a chamada do retorno de chamada antes de prosseguir para a próxima atividade.

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
nome | Nome da atividade de Web Hook | String | Sim |
tipo | Deve ser definido como **webhook**. | String | Sim |
método | Método de API REST para o ponto de extremidade de destino. | Strings. Tipos com suporte: ' POST ' | Sim |
url | Caminho e ponto de extremidade de destino | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres). | Sim |
conector | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "headers": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Sim, o cabeçalho Content-Type é necessário. "headers": {"Content-Type": "Application/JSON"} |
conteúdo | Representa a carga que é enviada para o ponto de extremidade. | O corpo passado de volta para o URI de retorno de chamada deve ser um JSON válido. Consulte o esquema da carga de solicitação na seção [esquema de carga de solicitação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Sim |
autenticação | Método de autenticação usado para chamar o ponto de extremidade. Os tipos com suporte são "básico" ou "ClientCertificate". Para obter mais informações, consulte a seção [autenticação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Se a autenticação não for necessária, exclua essa propriedade. | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Não |
tempo limite | Quanto tempo a atividade aguardará até que &#39;o&#39; callBackUri seja invocado. Por quanto tempo a atividade aguardará o ' callBackUri ' ser invocado. O valor padrão é 10mins ("00:10:00"). O formato é TimeSpan, ou seja, d. hh: mm: SS | String | Não |

## <a name="additional-notes"></a>Notas adicionais

Azure Data Factory passará uma propriedade adicional "callBackUri" no corpo para o ponto de extremidade da URL e esperará que esse URI seja invocado antes do valor de tempo limite especificado. Se o URI não for invocado, a atividade falhará com o status ' TimedOut '.

A própria atividade de gancho da Web falha somente quando a chamada para o ponto de extremidade personalizado falha. Qualquer mensagem de erro pode ser adicionada ao corpo do retorno de chamada e usada em uma atividade subsequente.

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
