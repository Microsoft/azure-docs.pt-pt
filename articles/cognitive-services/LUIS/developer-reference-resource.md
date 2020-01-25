---
title: Recursos para desenvolvedores-Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: Os desenvolvedores têm APIs REST e SDKs para Reconhecimento vocal.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: diberry
ms.openlocfilehash: 2f351ac570080c83e78697bbca94340bb96cbcf7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716127"
---
# <a name="developer-resources-for-language-understanding"></a>Recursos para desenvolvedores para Reconhecimento vocal

Os desenvolvedores podem usar APIs REST e SDKs para Reconhecimento vocal.

## <a name="azure-resource-management"></a>Gerenciamento de recursos do Azure

Use a camada de gerenciamento de serviços cognitivas do Azure para criar, editar, listar e excluir o Reconhecimento vocal ou recurso de serviço cognitiva.

Encontre a documentação de referência com base na ferramenta:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Solicitações de criação e previsão de Reconhecimento vocal

O serviço de Reconhecimento vocal é acessado de um recurso do Azure que você precisa criar. Há dois recursos: recursos de ponto de extremidade de criação e previsão. Esses dois recursos permitem que você controle seus recursos do LUIS.

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

### <a name="rest-apis"></a>APIs REST

As APIS de ponto de extremidade de criação e de previsão estão disponíveis em APIs REST:

|Tipo|Versão|
|--|--|
|Criação|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[visualização v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|predição|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Linguagem |Documentação de referência|Pacote|Exemplos|Guias de Início Rápido|
|--|--|--|--|--|
|C#|[Criação de conteúdos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Criação de NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Exemplos do SDK do .net](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Criar e gerenciar aplicativo](sdk-authoring.md?pivots=programming-language-csharp)<br>[Ponto de extremidade de previsão de consulta](sdk-query-prediction-endpoint.md)|
|Go|[Criação e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Criação e previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Criação e previsão](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Criação do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Criação e previsão](luis-get-started-get-intent-from-rest.md)
|Node.js|[Criação de conteúdos](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Criação de NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Previsão de NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Criação e previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Criação e previsão](sdk-authoring.md?pivots=programming-language-python)|[Pontos](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Criação de conteúdos](sdk-authoring.md?pivots=programming-language-python)<br>[Previsão usando REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contentores

O Reconhecimento vocal (LUIS) fornece um [contêiner](luis-container-howto.md) para fornecer versões locais e contidas do seu aplicativo.

### <a name="export-and-import-formats"></a>Exportar e importar formatos

Reconhecimento vocal fornece a capacidade de gerenciar seu aplicativo e seus modelos em um formato JSON, o formato `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) e um pacote compactado para o contêiner reconhecimento vocal.

A importação e a exportação desses formatos estão disponíveis nas APIs e no portal do LUIS. O portal fornece importação e exportação como parte da lista de aplicativos e lista de versões.

## <a name="other-tools-and-sdks"></a>Outras ferramentas e SDKs

A estrutura de bot está disponível como [um SDK](https://github.com/Microsoft/botframework) em uma variedade de linguagens e como um serviço usando o [serviço de bot do Azure](https://dev.botframework.com/).

O bot Framework fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar com reconhecimento vocal, incluindo:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -criar modelos de reconhecimento de linguagem Luis usando arquivos de redução
* [CLI do Luis](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – criar e gerenciar seus aplicativos do Luis.ai
* [Expedição](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-gerenciar aplicativos pai e filho
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – gere C#/typescript classes de backup automaticamente para suas intenções e entidades de Luis.
* [Emulador de bot](https://github.com/Microsoft/BotFramework-Emulator/releases) -um aplicativo de área de trabalho que permite que os desenvolvedores de bot testem e depurem bots criados usando o SDK do bot Framework


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [códigos de erro http](luis-reference-response-codes.md) comuns
* [Documentação de referência](https://docs.microsoft.com/azure/index#pivot=sdkstools) para todas as APIs e SDKs
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) e [serviço de bot do Azure](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contêineres cognitivas](../cognitive-services-container-support.md)