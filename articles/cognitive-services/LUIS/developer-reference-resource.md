---
title: Recursos de desenvolvimento - Compreensão linguística
description: SDKs, REST APIs, CLI, ajudam-no a desenvolver aplicações de Compreensão linguística (LUIS) na sua linguagem de programação. Gerencie os seus recursos Azure e previsões LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0e8da4d490ef5c9afb2ac363b62f0f1a4fe66f5e
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133315"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Recursos de desenvolvimento de SDK, REST e CLI para a Compreensão da Linguagem (LUIS)

SDKs, REST APIs, CLI, ajudam-no a desenvolver aplicações de Compreensão linguística (LUIS) na sua linguagem de programação. Gerencie os seus recursos Azure e previsões LUIS.

## <a name="azure-resource-management"></a>Gestão de recursos Azure

Utilize a camada de Gestão de Serviços Cognitivos Azure para criar, editar, listar e eliminar o recurso De compreensão linguística ou serviço cognitivo.

Encontre documentação de referência com base na ferramenta:

* [CLI do Azure](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Pedidos de autoria e previsão de compreensão linguística

O serviço de Compreensão de Línguas é acedido a partir de um recurso Azure que precisa de criar. Existem dois recursos:

* Utilize o recurso **de autoria** para formação para criar, editar, treinar e publicar.
* Utilize a **previsão** para o tempo de funcionação para enviar o texto do utilizador e receber uma previsão.

Conheça o ponto final de [previsão V3.](luis-migration-api-v3.md)

Utilize o [código de amostra dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-quickstart-code) para aprender e utilizar as tarefas mais comuns.

### <a name="rest-specifications"></a>Especificações DE REPOUSO

As [especificações LUIS REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), juntamente com todas as [especificações Azure REST,](https://github.com/Azure/azure-rest-api-specs)estão disponíveis publicamente no GitHub.

### <a name="rest-apis"></a>APIs REST

Tanto a autoria como a previsão do ponto final APIS estão disponíveis a partir de APIs REST:

|Tipo|Versão|
|--|--|
|Criação|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[pré-visualização V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Predição|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>PONTOS de final de DESCANSO

A LUIS tem atualmente 2 tipos de pontos finais:

* **autoria** no ponto final de formação
* **previsão** de consulta sobre o ponto final de tempo de execução.

|Objetivo|URL|
|--|--|
|V2 Autoria no ponto final da formação|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3 Autoria no ponto final da formação|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Previsão V2 - todas as previsões no ponto final do tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Previsão V3 - previsão de versões no ponto final do tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Previsão V3 - previsão de slot no ponto final do tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

A tabela seguinte explica os parâmetros, denotados com aparelhos encaracolados, `{}` na tabela anterior.

|Parâmetro|Objetivo|
|--|--|
|`your-resource-name`|Nome do recurso Azure|
|`q` ou `query`|texto de expressão enviado a partir de aplicação do cliente, como chat bot|
|`version`|Nome da versão de 10 caracteres|
|`slot`| `production` ou `staging`|

### <a name="rest-query-string-parameters"></a>Parâmetros de cadeia de consulta DE REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Esquema da aplicação

O [esquema da aplicação](app-schema-definition.md) é importado e exportado em formato `.json` ou `.lu` formato.

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Linguagem |Documentação de referência|Pacote|Guias de Início Rápido|
|--|--|--|--|
|C#|[Criação](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Predição](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Autoria nuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Criação](azure-sdk-quickstart.md?pivots=programming-language-csharp)<br>[Previsão de consulta](azure-sdk-quickstart.md?pivots=programming-language-csharp)|
|Go|[Autoria e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Autoria e previsão](/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Autoria de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Criação](/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Predição](/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Autoria do NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Previsão de NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Criação](azure-sdk-quickstart.md?pivots=programming-language-javascript)<br>[Predição](azure-sdk-quickstart.md?pivots=programming-language-javascript)|
|Python|[Autoria e previsão](azure-sdk-quickstart.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação](azure-sdk-quickstart.md?pivots=programming-language-python)<br>[Predição](azure-sdk-quickstart.md?pivots=programming-language-python)|


### <a name="containers"></a>Contentores

A Language Understanding (LUIS) fornece um [recipiente](luis-container-howto.md) para fornecer no local e versões contidas da sua aplicação.

### <a name="export-and-import-formats"></a>Formatos de exportação e importação

A Language Understanding fornece a capacidade de gerir a sua aplicação e os seus modelos num formato JSON, no `.LU` formato[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)e num pacote comprimido para o recipiente de Compreensão linguística.

Importar e exportar estes formatos está disponível nas APIs e no portal LUIS. O portal fornece importação e exportação como parte da lista de aplicações e versões.

## <a name="workshops"></a>Workshops

* GitHub: (Workshop) [Conversação-AI : NLU usando LUIS](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Ferramentas de integração contínua

* GitHub: (Pré-visualização) [Desenvolver uma aplicação LUIS usando práticas de DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Ferramentas que suportam integração contínua e implementação para serviços NLU.

## <a name="bot-framework-tools"></a>Ferramentas de estrutura de bot

A estrutura bot está disponível como [SDK](https://github.com/Microsoft/botframework) em uma variedade de idiomas e como um serviço usando [o Azure Bot Service](https://dev.botframework.com/).

A estrutura do bot fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar na compreensão da linguagem, incluindo:
* [Emulador Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) - uma aplicação para desktop que permite aos desenvolvedores de bots testar e depurar bots construídos usando o Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - uma ferramenta integrada de desenvolvimento para desenvolvedores e equipas multidisciplinares para construir bots e experiências de conversação com o Microsoft Bot Framework
* [Amostras de estrutura de](https://github.com/microsoft/botbuilder-samples) bot - em #C, JavaScript, TypeScript e Python

## <a name="next-steps"></a>Passos seguintes

* Conheça os [códigos](luis-reference-response-codes.md) de erro HTTP comuns
* [Documentação de referência](../../index.yml) para todos os APIs e SDKs
* [Estrutura bot](https://github.com/Microsoft/botbuilder-dotnet) e [serviço Azure Bot](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contentores cognitivos](../cognitive-services-container-support.md)
