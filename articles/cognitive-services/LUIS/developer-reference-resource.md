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

Conheça o ponto final da [previsão V3.](luis-migration-api-v3.md)

### <a name="rest-apis"></a>APIs REST

As APIS de ponto de extremidade de criação e de previsão estão disponíveis em APIs REST:

|Tipo|Versão|
|--|--|
|Criação de conteúdos|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[pré-visualização V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|predição|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Idioma |Documentação de referência|Pacote|Amostras|Inícios rápidos|
|--|--|--|--|--|
|C#|[Criação de conteúdos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Autor nuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net Amostras de SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Criar e gerir app](sdk-authoring.md?pivots=programming-language-csharp)<br>[Ponto final da previsão de consulta](sdk-query-prediction-endpoint.md)|
|Ir|[Autoria e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Autoria e Previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Autoria e previsão](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Autoria de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Autoria e Previsão](luis-get-started-get-intent-from-rest.md)
|Node.js|[Criação de conteúdos](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Autoria nPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Previsão npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Autoria e Previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Autoria e previsão](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação de conteúdos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Criação de conteúdos](sdk-authoring.md?pivots=programming-language-python)<br>[Previsão usando REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contentores

A Language Understanding (LUIS) fornece um [recipiente](luis-container-howto.md) para fornecer no local e versões contidas da sua aplicação.

### <a name="export-and-import-formats"></a>Exportar e importar formatos

A Compreensão de Idiomas fornece a capacidade de gerir a sua app e os seus modelos num formato JSON, o formato `.LU`[(LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)e um pacote comprimido para o recipiente de Compreensão de Línguas.

A importação e a exportação desses formatos estão disponíveis nas APIs e no portal do LUIS. O portal fornece importação e exportação como parte da lista de aplicativos e lista de versões.

## <a name="other-tools-and-sdks"></a>Outras ferramentas e SDKs

O quadro bot está disponível como [Um SDK](https://github.com/Microsoft/botframework) em uma variedade de idiomas e como um serviço usando [o Serviço Bot Azure](https://dev.botframework.com/).

A estrutura bot fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar na compreensão da linguagem, incluindo:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Construir modelos de compreensão linguística LUIS usando ficheiros de marcação
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Crie e gerencie as suas aplicações LUIS.ai
* [Despacho](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- gerencie aplicativos para pais e filhos
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Auto C#gera suporte /Aulas de scripts para as suas intenções e entidades LUIS.
* [Emulador bot](https://github.com/Microsoft/BotFramework-Emulator/releases) - uma aplicação de desktop que permite aos desenvolvedores de bots testar e depurar bots construídos usando o Bot Framework SDK


## <a name="next-steps"></a>Passos Seguintes

* Conheça os [códigos](luis-reference-response-codes.md) de erro http comuns
* [Documentação de referência](https://docs.microsoft.com/azure/index#pivot=sdkstools) para todas as APIs e SDKs
* [Quadro bot](https://github.com/Microsoft/botbuilder-dotnet) e [Serviço Bot Azure](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Recipientes Cognitivos](../cognitive-services-container-support.md)