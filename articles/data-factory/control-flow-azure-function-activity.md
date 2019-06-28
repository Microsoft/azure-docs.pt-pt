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
ms.openlocfilehash: dfdfb9e38f16d0077175587933b0800b87cc1931
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144119"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Actividade de função do Azure no Azure Data Factory

A actividade de função do Azure permite-lhe executar [as funções do Azure](../azure-functions/functions-overview.md) no pipeline do Data Factory. Para executar uma função do Azure, terá de criar uma ligação de serviço ligado e uma atividade que especifica a função do Azure que pretende executar.

Para uma introdução de oito minutos e uma demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço ligado de função do Azure

O tipo de retorno da função do Azure tem de ser válido `JObject`. (Lembre-se [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) é *não* um `JObject`.) Qualquer tipo de retorno diferente de `JObject` falhar e gera o erro de utilizador *conteúdo de resposta não é um válido JObject*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| type   | A propriedade de tipo tem de ser definida como: **AzureFunction** | sim |
| url da aplicação de função | URL da aplicação de função do Azure. O formato é `https://<accountname>.azurewebsites.net`. Este URL é o valor sob **URL** secção ao visualizar a aplicação de funções no portal do Azure  | sim |
| tecla de função | Chave de acesso para a função do Azure. Clique no **gerir** secção para obter a respetiva função e copie-o a **tecla de função** ou o **chave de anfitrião**. Saiba mais aqui: [Enlaces e acionadores de HTTP de funções do Azure](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Actividade de função do Azure

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| name  | Nome da atividade no pipeline  | String | sim |
| type  | Tipo de atividade é 'AzureFunctionActivity' | String | sim |
| Serviço ligado | O serviço de função do Azure ligado para a aplicação de funções do Azure correspondente  | Referência de serviço ligado | sim |
| Nome da função  | Nome da função na aplicação de função do Azure que chama esta atividade | String | sim |
| method  | Método de REST API para a chamada de função | Cadeia de caracteres de tipos suportados: "GET", "POST", "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e o tipo de um pedido: "cabeçalhos": {"Accept-Language": "en-us", "Content-Type": "application/json"} | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) | Não |
| Corpo  | corpo que é enviado com o pedido para o método da api de função  | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) ou o objeto.   | Necessário para métodos PUT/POST |
|   |   |   | |

Ver o esquema do payload de pedido no [esquema de payload de pedido](control-flow-web-activity.md#request-payload-schema) secção.

## <a name="routing-and-queries"></a>Encaminhamento e consultas

A actividade de função do Azure suporta **encaminhamento**. Por exemplo, se a sua função do Azure tem o ponto final `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, em seguida, o `functionName` para utilizar na atividade de função do Azure é `<functionName>/<value>`. É possível parametrizar esta função para fornecer o desejado `functionName` em tempo de execução.

A actividade de função do Azure também suporta **consultas**. Uma consulta tem de ser incluído como parte do `functionName`. Por exemplo, quando o nome da função é `HttpTriggerCSharp` e a consulta que pretende incluir `name=hello`, em seguida, pode construir o `functionName` na atividade de função do Azure como `HttpTriggerCSharp?name=hello`. Esta função pode ser parametrizada, pelo que o valor pode ser determinado em runtime.

## <a name="timeout-and-long-running-functions"></a>Tempo limite e funções de execução longa

Azure funções expire após 230 segundos, independentemente do `functionTimeout` definição já configurada nas definições. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar este comportamento, seguem um padrão assíncrono ou utilize funções duráveis. A vantagem de funções durável é que eles oferecem a seu próprio mecanismo de controlo de estado, para que não tenha de implementar sua própria.

Saiba mais sobre as funções durável na [este artigo](../azure-functions/durable/durable-functions-overview.md). Pode configurar uma atividade de função do Azure para chamar a função duráveis, o que irá devolver uma resposta com um URI diferente, tal como [neste exemplo](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Uma vez que `statusQueryGetUri` devolve o estado de HTTP 202 enquanto a função está em execução, pode consultar o estado da função através de uma atividade de Web. Basta configurar uma atividade Web com o `url` campo definido como `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Quando tiver concluído a função duráveis, o resultado da função será a saída da atividade Web.


## <a name="sample"></a>Exemplo

Pode encontrar um exemplo de uma fábrica de dados que utiliza uma função do Azure para extrair o conteúdo de um arquivo tar [aqui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as atividades no Data Factory no [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md).
