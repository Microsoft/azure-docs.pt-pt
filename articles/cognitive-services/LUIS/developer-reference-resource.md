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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 8accac7fe6068007180403fdab27013da161b28c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437185"
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

* Documentação de [referência](https://go.microsoft.com/fwlink/?linkid=2092087) de criação
* [Documentação de referência](https://go.microsoft.com/fwlink/?linkid=2092356) de tempo de execução de previsão

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Linguagem |Documentação de referência|Pacote|Exemplos|Inícios rápidos|
|--|--|--|--|--|
|C#|[Criação de conteúdos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Criação de NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Exemplos do SDK do .net](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Criar e gerenciar aplicativo](sdk-csharp-quickstart-authoring-app.md)<br>[Ponto de extremidade de previsão de consulta](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Criação e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Criando usando REST](luis-get-started-go-add-utterance.md)<br>[Previsão usando REST](luis-get-started-go-get-intent.md)|
|Java|[Criação e previsão](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Criação do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Criação de conteúdos](luis-get-started-java-add-utterance.md)<br>[Previsão](luis-get-started-java-get-intent.md)
|Node.js|[Criação de conteúdos](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Criação de NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[Previsão de NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Criando usando REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Previsão usando REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Criação e previsão](sdk-python-quickstart-authoring-app.md)|[Pontos](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Criação de conteúdos](sdk-python-quickstart-authoring-app.md)<br>[Previsão usando REST](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Outras ferramentas e SDKs

A estrutura de bot está disponível como [um SDK](https://github.com/Microsoft/botframework) em uma variedade de linguagens e como um serviço usando o [serviço de bot do Azure](https://dev.botframework.com/). 

O bot Framework fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar com reconhecimento vocal, incluindo:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -criar modelos de reconhecimento de linguagem Luis usando arquivos de redução
* [CLI do Luis](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – criar e gerenciar seus aplicativos do Luis.ai
* [Expedição](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-gerenciar aplicativos pai e filho
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – gere C#/typescript classes de backup automaticamente para suas intenções e entidades de Luis.
* [Emulador de bot](https://github.com/Microsoft/BotFramework-Emulator/releases) -um aplicativo de área de trabalho que permite que os desenvolvedores de bot testem e depurem bots criados usando o SDK do bot Framework


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [códigos de erro http](luis-reference-response-codes.md) comuns