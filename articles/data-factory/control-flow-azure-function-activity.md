---
title: Actividade de função do Azure na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como utilizar a atividade de função do Azure para executar uma função do Azure no pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: b98d20a1f96a6ab4a0dc72330e85fdc98ba04eae
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576383"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Actividade de função do Azure no Azure Data Factory

A actividade de função do Azure permite-lhe executar [as funções do Azure](../azure-functions/functions-overview.md) no pipeline do Data Factory. Para executar uma função do Azure, terá de criar uma ligação de serviço ligado e uma atividade que especifica a função do Azure que pretende executar.

Para uma introdução de oito minutos e uma demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço ligado de função do Azure

O tipo de retorno da função do Azure tem de ser válido `JObject`. (Lembre-se [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) é *não* um `JObject`.) Qualquer tipo de retorno diferente de `JObject` falhar e gera o erro de utilizador genérica *ponto final de chamada de erro*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo   | A propriedade de tipo tem de ser definida como: **AzureFunction** | sim |
| url da aplicação de função | URL da aplicação de função do Azure. O formato é `https://<accountname>.azurewebsites.net`. Este URL é o valor sob **URL** secção ao visualizar a aplicação de funções no portal do Azure  | sim |
| tecla de função | Chave de acesso para a função do Azure. Clique no **gerir** secção para obter a respetiva função e copie-o a **tecla de função** ou o **chave de anfitrião**. Saiba mais aqui: [Enlaces e acionadores de HTTP de funções do Azure](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Actividade de função do Azure

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| name  | Nome da atividade no pipeline  | String | sim |
| tipo  | Tipo de atividade é 'AzureFunctionActivity' | String | sim |
| Serviço ligado | O serviço de função do Azure ligado para a aplicação de funções do Azure correspondente  | Referência de serviço ligado | sim |
| Nome da função  | Nome da função na aplicação de função do Azure que chama esta atividade | String | sim |
| método  | Método de REST API para a chamada de função | Cadeia de caracteres de tipos suportados: "GET", "POST", "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e o tipo de um pedido: "cabeçalhos": {"Accept-Language": "en-us", "Content-Type": "application/json"} | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) | Não |
| corpo  | corpo que é enviado com o pedido para o método da api de função  | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) ou o objeto.   | Necessário para métodos PUT/POST |
|   |   |   | |

Ver o esquema do payload de pedido no [esquema de payload de pedido](control-flow-web-activity.md#request-payload-schema) secção.

## <a name="more-info"></a>Mais informações

A actividade de função do Azure suporta **encaminhamento**. Por exemplo, se a sua aplicação utiliza o encaminhamento seguintes - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` - o `functionName` é `functionName/{value}`, que é possível parametrizar para fornecer o desejado `functionName` em tempo de execução.

A actividade de função do Azure também suporta **consultas**. Tem de fazer parte de uma consulta a `functionName` – por exemplo, `HttpTriggerCSharp2?name=hello` – onde o `function name` é `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as atividades no Data Factory no [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md).
