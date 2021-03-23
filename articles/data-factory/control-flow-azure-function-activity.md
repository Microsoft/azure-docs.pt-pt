---
title: Atividade de função Azure na Fábrica de Dados Azure
description: Saiba como utilizar a atividade da Função Azure para executar uma Função Azure num pipeline da Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 13f00907737a99bc5dcd8c21d660ce83aa681908
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783800"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade da função Azure na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
A atividade Azure Function permite-lhe executar [funções Azure](../azure-functions/functions-overview.md) num oleoduto de Fábrica de Dados. Para executar uma Função Azure, é necessário criar uma ligação de serviço ligada e uma atividade que especifique a Função Azure que planeia executar.

Para uma introdução de oito minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço ligado à função Azure

O tipo de devolução da função Azure tem de ser válido `JObject` . (Tenha em mente que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é `JObject` um.) Qualquer tipo de devolução que não `JObject` falhe e eleva o conteúdo de resposta ao erro do utilizador *não é um JObject válido*.

| **Propriedade** | **Descrição** | **Obrigatório** |
| --- | --- | --- |
| tipo   | A propriedade tipo deve ser definida para: **AzureFunction** | sim |
| url de aplicação de função | URL para a App de Função Azure. O formato é `https://<accountname>.azurewebsites.net` . Este URL é o valor na secção **URL** ao visualizar a sua App de Função no portal Azure  | sim |
| chave de função | Chave de acesso para a Função Azure. Clique na secção **'Gerir'** para a respetiva função e copie a **tecla 'Função'** ou a **tecla 'Anfitrião'.** Saiba mais aqui: [Funções Azure HTTP detona e encadernações](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade da Função Azure

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Obrigatório** |
| --- | --- | --- | --- |
| name  | Nome da atividade no oleoduto  | String | sim |
| tipo  | Tipo de atividade é 'AzureFunctionActivity' | String | sim |
| serviço ligado | O serviço ligado à Função Azure para a app de função Azure correspondente  | Referência de serviço ligada | sim |
| nome de função  | Nome da função na App de Função Azure a que esta atividade chama | String | sim |
| método  | Método DE API REST para a chamada de função | Tipos suportados por cordas: "GET", "POST", "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e escrever num pedido: "cabeçalhos": { "Aceitar linguagem": "en-us", "Content-Type": "application/json" } | Corda (ou expressão com resultadoType de corda) | No |
| body  | corpo que é enviado juntamente com o pedido para o método função api  | Corda (ou expressão com resultadoTipo de corda) ou objeto.   | Necessário para os métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga útil do pedido na secção [de esquemas de carga útil do Pedido.](control-flow-web-activity.md#request-payload-schema)

## <a name="routing-and-queries"></a>Encaminhamento e consultas

A Atividade de Função Azure suporta **o encaminhamento**. Por exemplo, se a sua Função Azure tiver o ponto  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` final, então `functionName` a utilização na Função Azul é `<functionName>/<value>` . Pode parametrizar esta função para fornecer o desejado `functionName` no tempo de execução.

A Atividade de Função Azure também suporta **consultas.** Uma consulta tem de ser incluída como parte do `functionName` . Por exemplo, quando o nome da função é `HttpTriggerCSharp` e a consulta que pretende incluir é , `name=hello` então pode construir a atividade da `functionName` função Azure como `HttpTriggerCSharp?name=hello` . Esta função pode ser parametrizada para que o valor possa ser determinado no tempo de execução.

## <a name="timeout-and-long-running-functions"></a>Funções de tempo e de longa duração

As funções do Azure são de 230 segundos, independentemente da `functionTimeout` definição configurada nas definições. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar este comportamento, siga um padrão assíduo ou use Funções Duráveis. O benefício das Funções Duradouras é que eles oferecem o seu próprio mecanismo de rastreio do Estado, para que não tenhas de implementar o teu próprio.

Saiba mais sobre Funções Duradouras [neste artigo.](../azure-functions/durable/durable-functions-overview.md) Pode configurar uma Atividade de Função Azure para chamar a Função Durável, que devolverá uma resposta com um URI diferente, como [este exemplo](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Como `statusQueryGetUri` devolve o Estado HTTP 202 durante o funcionamento da função, pode sondar o estado da função utilizando uma Atividade Web. Basta configurar uma Atividade Web com o `url` campo definido para `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . Quando a Função Durável estiver concluída, a saída da função será a saída da Atividade Web.


## <a name="sample"></a>Sample

Pode encontrar uma amostra de uma Fábrica de Dados que utiliza uma Função Azure para extrair o conteúdo de um ficheiro de alcatrão [aqui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as atividades na Fábrica de Dados em [Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md)
