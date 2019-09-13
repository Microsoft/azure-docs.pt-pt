---
title: Atividade do Azure Functions no Azure Data Factory | Microsoft Docs
description: Saiba como usar a atividade do Azure function para executar uma função do Azure em um pipeline Data Factory
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
ms.openlocfilehash: a3499637fb5320afe80bf4eefa634173db31f1b6
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931853"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade do Azure Functions no Azure Data Factory

A atividade de funções do Azure permite que você execute [Azure Functions](../azure-functions/functions-overview.md) em um pipeline de data Factory. Para executar uma função do Azure, você precisa criar uma conexão de serviço vinculado e uma atividade que especifica a função do Azure que você planeja executar.

Para uma introdução e uma demonstração de oito minutos desse recurso, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço vinculado de funções do Azure

O tipo de retorno da função do Azure deve ser válido `JObject`. (Tenha em mente que [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) o JArray *não* é `JObject`um.) Qualquer tipo de retorno diferente `JObject` de falha e gera o conteúdo de resposta de erro do usuário *não é um JObject válido*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| type   | A propriedade Type deve ser definida como: **AzureFunction** | sim |
| URL do aplicativo de funções | URL para o Aplicativo de funções do Azure. O formato `https://<accountname>.azurewebsites.net`é. Essa URL é o valor na seção de **URL** ao exibir sua Aplicativo de funções no portal do Azure  | sim |
| chave de função | Chave de acesso para a função do Azure. Clique na seção **gerenciar** para a respectiva função e copie a **chave de função** ou a chave de **host**. Saiba mais aqui: [Azure Functions gatilhos e associações HTTP](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade da função do Azure

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| name  | Nome da atividade no pipeline  | Cadeia | sim |
| type  | O tipo de atividade é ' AzureFunctionActivity ' | Cadeia | sim |
| serviço vinculado | O serviço vinculado da função do Azure para o Aplicativo de funções do Azure correspondente  | Referência de serviço vinculado | sim |
| Nome da função  | Nome da função no Aplicativo de funções do Azure que esta atividade chama | Cadeia | sim |
| method  | Método de API REST para a chamada de função | Tipos de cadeia de caracteres com suporte: "GET", "POST" E "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "headers": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"} | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Não |
| corpo  | corpo enviado junto com a solicitação para o método de API de função  | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) ou objeto.   | Necessário para os métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga de solicitação na seção [esquema](control-flow-web-activity.md#request-payload-schema) de carga de solicitação.

## <a name="routing-and-queries"></a>Roteamento e consultas

A atividade de funções do Azure dá suporte ao **Roteamento**. Por exemplo, se a função do Azure tiver o `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`ponto de extremidade `functionName` , o para usar na atividade de função `<functionName>/<value>`do Azure é. Você pode parametrizar essa função para fornecer as desejadas `functionName` em tempo de execução.

A atividade de funções do Azure também dá suporte a **consultas**. Uma consulta deve ser incluída como parte do `functionName`. Por exemplo, quando o nome da função `HttpTriggerCSharp` é e a consulta que você deseja incluir é `name=hello`, você pode construir o `functionName` na atividade de função do Azure como `HttpTriggerCSharp?name=hello`. Essa função pode ser parametrizada para que o valor possa ser determinado em tempo de execução.

## <a name="timeout-and-long-running-functions"></a>Funções de tempo limite e execução longa

Azure Functions o `functionTimeout` tempo limite após 230 segundos, independentemente da configuração que você definiu nas configurações. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar esse comportamento, siga um padrão assíncrono ou use Durable Functions. A vantagem do Durable Functions é que eles oferecem seu próprio mecanismo de controle de estado, portanto, você não precisará implementar o seu.

Saiba mais sobre Durable Functions neste [artigo](../azure-functions/durable/durable-functions-overview.md). Você pode configurar uma atividade do Azure function para chamar a função durável, que retornará uma resposta com um URI diferente, como [Este exemplo](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Como `statusQueryGetUri` o retorna o status http 202 enquanto a função está em execução, você pode sondar o status da função usando uma atividade da Web. Basta configurar uma atividade da Web com o `url` campo definido como `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Quando a função durável for concluída, a saída da função será a saída da atividade da Web.


## <a name="sample"></a>Exemplo

Você pode encontrar um exemplo de um Data Factory que usa uma função do Azure para extrair o conteúdo de um arquivo tar [aqui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as atividades em Data Factory em [pipelines e atividades no Azure data Factory](concepts-pipelines-activities.md).
