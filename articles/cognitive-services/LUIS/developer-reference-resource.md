---
title: Recursos de desenvolvimento - Compreensão da Linguagem
description: SDKs, REST APIs, CLI, ajudam-no a desenvolver aplicações de Compreensão de Línguas (LUIS) na sua linguagem de programação. Gerencie os seus recursos Azure e as previsões do LUIS.
ms.topic: reference
ms.date: 05/19/2020
ms.openlocfilehash: c712a81e5b786ac980a0c48d358fef4caf2e7597
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758066"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Recursos de desenvolvimento sdK, REST e CLI para a compreensão da linguagem (LUIS)

SDKs, REST APIs, CLI, ajudam-no a desenvolver aplicações de Compreensão de Línguas (LUIS) na sua linguagem de programação. Gerencie os seus recursos Azure e as previsões do LUIS.

## <a name="azure-resource-management"></a>Gestão de recursos azure

Utilize a camada de Gestão de Serviços Cognitivos Azure para criar, editar, listar e eliminar o recurso de Compreensão linguística ou serviço cognitivo.

Encontre documentação de referência com base na ferramenta:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Pedidos de autore de previsão de compreensão linguística

O serviço de Compreensão de Línguas é acedido a partir de um recurso Azure que você precisa criar. Há dois recursos:

* Utilize o recurso **de autoria** para a formação para criar, editar, treinar e publicar.
* Utilize a **previsão** para o tempo de execução para enviar o texto do utilizador e receber uma previsão.

Conheça o ponto final da [previsão V3.](luis-migration-api-v3.md)

Utilize o código de [amostra dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-quickstart-code) para aprender e utilizar as tarefas mais comuns.

### <a name="rest-specifications"></a>Especificações de REPOUSO

As [especificações LUIS REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), juntamente com todas as [especificações do Azure REST](https://github.com/Azure/azure-rest-api-specs), estão disponíveis publicamente no GitHub.

### <a name="rest-apis"></a>APIs REST

Tanto a autoria como o ponto final de previsão APIS estão disponíveis a partir de APIs REST:

|Tipo|Versão|
|--|--|
|Criação|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[pré-visualização V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Previsão|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Pontos Finais REST

A LUIS tem atualmente 2 tipos de pontos finais:

* **autoria** sobre o ponto final de treino
* **previsão** de consulta no ponto final do tempo de execução.

|Objetivo|URL|
|--|--|
|V2 Autoria no fim da formação|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3 Autoria no fim da formação|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Previsão V2 - todas as previsões sobre o ponto final do tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Previsão V3 - previsão de versões no ponto final do tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Previsão V3 - previsão de slot no ponto final do tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

A tabela a seguir explica os parâmetros, denotados com aparelhos encaracolados, `{}` na tabela anterior.

|Parâmetro|Objetivo|
|--|--|
|`your-resource-name`|Nome de recurso Azure|
|`q` ou `query`|texto de expressão enviado da aplicação do cliente, como chat bot|
|`version`|Nome de versão de 10 caracteres|
|`slot`| `production` ou `staging`|

## <a name="app-schema"></a>Esquema da aplicação

O esquema da [aplicação](app-schema-definition.md) é importado e exportado num `.json` ou `.lu` formato.

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Linguagem |Documentação de referência|Pacote|Amostras|Guias de Início Rápido|
|--|--|--|--|--|
|C#|[Criação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Autor nuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Exemplos de SDK .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Criar e gerir uma aplicação](sdk-authoring.md?pivots=programming-language-csharp)<br>[Ponto final de predição da consulta](sdk-query-prediction-endpoint.md)|
|Ir|[Autoria e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Autoria e Previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Autoria e previsão](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Autoria de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Autoria e Previsão](luis-get-started-get-intent-from-rest.md)
|Node.js|[Criação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Autoria nPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Previsão npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Autoria e Previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Autoria e previsão](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Criação](sdk-authoring.md?pivots=programming-language-python)<br>[Previsão usando REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contentores

A Language Understanding (LUIS) fornece um [recipiente](luis-container-howto.md) para fornecer no local e versões contidas da sua aplicação.

### <a name="export-and-import-formats"></a>Formatos de exportação e importação

A Compreensão de Idiomas fornece a capacidade de gerir a sua app e os seus modelos num formato JSON, o `.LU` formato[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)e um pacote comprimido para o recipiente de Compreensão de Línguas.

A importação e exportação destes formatos está disponível a partir das APIs e do portal LUIS. O portal fornece importação e exportação como parte da lista de Apps e Versões.

## <a name="workshops"></a>Workshops

* GitHub: (Workshop) [Conversational-AI : NLU usando LUIS](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Ferramentas de integração contínua

* GitHub: (Pré-visualização) [Desenvolvimento de uma aplicação LUIS utilizando práticas de DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Ferramentas que suportam a integração contínua e a implantação dos serviços NLU.

## <a name="bot-framework-tools"></a>Ferramentas bot-quadro

O quadro bot está disponível como [Um SDK](https://github.com/Microsoft/botframework) em uma variedade de idiomas e como um serviço usando [o Serviço Bot Azure](https://dev.botframework.com/).

A estrutura bot fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar na compreensão da linguagem, incluindo:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Construir modelos de compreensão linguística LUIS usando ficheiros de marcação
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Crie e gerencie as suas aplicações LUIS.ai
* [Despacho](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- gerencie aplicativos para pais e filhos
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Auto gera aulas de c#/Typescript para as suas intenções e entidades LUIS.
* [Bot Framework emulador](https://github.com/Microsoft/BotFramework-Emulator/releases) - uma aplicação de ambiente de trabalho que permite aos desenvolvedores de bots testar e depurar bots construídos usando o Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - uma ferramenta de desenvolvimento integrada para programadores e equipas multidisciplinares para construir bots e experiências de conversação com o Microsoft Bot Framework
* [Amostras de quadro](https://github.com/microsoft/botbuilder-samples) bot - em #C, JavaScript, TypeScript e Python
## <a name="next-steps"></a>Passos seguintes

* Conheça os [códigos](luis-reference-response-codes.md) de erro http comuns
* [Documentação de referência](https://docs.microsoft.com/azure/index) para todas as APIs e SDKs
* [Quadro bot](https://github.com/Microsoft/botbuilder-dotnet) e [Serviço Bot Azure](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Recipientes Cognitivos](../cognitive-services-container-support.md)
