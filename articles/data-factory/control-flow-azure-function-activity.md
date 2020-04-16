---
title: Atividade de função azure na fábrica de dados azure
description: Saiba como utilizar a atividade da Função Azure para executar uma Função Azure num oleoduto de Fábrica de Dados
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415314"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade da Função Azure na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
A atividade da Função Azure permite-lhe executar [funções Azure](../azure-functions/functions-overview.md) num oleoduto de Fábrica de Dados. Para executar uma Função Azure, é necessário criar uma ligação de serviço ligada e uma atividade que especifique a Função Azure que pretende executar.

Para uma introdução de oito minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço ligado à Função Azure

O tipo de devolução da função `JObject`Azure tem de ser válido . (Tenha em mente que [jArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é uma `JObject`.) Qualquer tipo de `JObject` devolução que não falhe e eleva o conteúdo de resposta de erro do utilizador *não é um JObject válido*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo   | A propriedade tipo deve ser definida para: **AzureFunction** | sim |
| url de aplicativo de função | URL para a App função Azure. O `https://<accountname>.azurewebsites.net`formato é . Este URL é o valor da secção **URL** ao visualizar a sua App de Funções no portal Azure  | sim |
| chave de função | Chave de acesso para a Função Azure. Clique na secção **Gerir** para a respetiva função e copie a Chave de **Função** ou a **tecla anfitrião**. Saiba mais aqui: [Azure Functions HTTP gatilhos e encadernações](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade da Função Azure

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nome  | Nome da atividade no oleoduto  | String | sim |
| tipo  | Tipo de atividade é 'AzureFunctionActivity' | String | sim |
| serviço ligado | O serviço ligado à função Azure para a app de função azure correspondente  | Referência de serviço ligada | sim |
| nome da função  | Nome da função na App função Azure que esta atividade chama | String | sim |
| método  | Método DE API REST para a chamada de função | Tipos suportados por cordas: "GET", "POST", "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e escrever num pedido: "cabeçalhos": { "Aceitar-Linguagem": "en-us", "Content-Type": "application/json" } | Corda (ou expressão com resultadoTipo de corda) | Não |
| body  | corpo que é enviado juntamente com o pedido para o método api função  | Corda (ou expressão com resultadoTipo de corda) ou objeto.   | Necessário para métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga útil do pedido na secção esquema de carga útil [solicitação.](control-flow-web-activity.md#request-payload-schema)

## <a name="routing-and-queries"></a>Encaminhamento e consultas

A Atividade de Função Azure suporta **o encaminhamento**. Por exemplo, se a sua Função Azure tiver o ponto `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`final, então a `functionName` utilização na Atividade de Função Azure é `<functionName>/<value>`. Pode parametrizar esta função `functionName` para fornecer o desejado no tempo de funcionamento.

A Atividade de Função Azure também suporta **consultas.** Uma consulta tem de ser incluída `functionName`como parte do . Por exemplo, quando o `HttpTriggerCSharp` nome da função é `name=hello`e a consulta `functionName` que pretende incluir é, então pode construir a atividade de função Azure como `HttpTriggerCSharp?name=hello`. Esta função pode ser parametrizada para que o valor possa ser determinado no tempo de funcionamento.

## <a name="timeout-and-long-running-functions"></a>Funções de timeout e de longa duração

Funções Azure horários após 230 `functionTimeout` segundos, independentemente da definição configurada nas definições. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar este comportamento, siga um padrão de asincronização ou use Funções Duráveis. O benefício das Funções Duráveis é que eles oferecem o seu próprio mecanismo de rastreio do Estado, por isso não terás de implementar o teu próprio.

Saiba mais sobre as Funções Duráveis [neste artigo.](../azure-functions/durable/durable-functions-overview.md) Pode configurar uma Atividade de Função Azure para chamar a Função Durável, que devolverá uma resposta com um URI diferente, como [este exemplo.](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery) Como `statusQueryGetUri` devolve o status HTTP 202 enquanto a função está em execução, pode sondar o estado da função utilizando uma Atividade Web. Basta configurar uma Atividade `url` Web `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`com o campo definido para . Quando a Função Durável estiver concluída, a saída da função será a saída da Atividade Web.


## <a name="sample"></a>Sample

Pode encontrar aqui uma amostra de uma Fábrica de Dados que utiliza uma Função Azure para extrair o conteúdo de um [ficheiro](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)de alcatrão .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre atividades na Data Factory em [Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md)
