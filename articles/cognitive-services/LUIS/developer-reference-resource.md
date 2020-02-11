---
title: Recursos para desenvolvedores-Reconhecimento vocal
description: SDKs, REST APIs, CLI, ajudam-no a desenvolver aplicações de Compreensão de Línguas (LUIS) na sua linguagem de programação. Gerencie os seus recursos Azure e as previsões do LUIS.
ms.topic: reference
ms.date: 02/09/2020
ms.openlocfilehash: ed869b7022e43b8ecf8c1f05bb3c6f0919076818
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119972"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Recursos de desenvolvimento sdK, REST e CLI para a compreensão da linguagem (LUIS)

SDKs, REST APIs, CLI, ajudam-no a desenvolver aplicações de Compreensão de Línguas (LUIS) na sua linguagem de programação. Gerencie os seus recursos Azure e as previsões do LUIS. 

## <a name="azure-resource-management"></a>Gerenciamento de recursos do Azure

Use a camada de gerenciamento de serviços cognitivas do Azure para criar, editar, listar e excluir o Reconhecimento vocal ou recurso de serviço cognitiva.

Encontre a documentação de referência com base na ferramenta:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Solicitações de criação e previsão de Reconhecimento vocal

O serviço de Reconhecimento vocal é acessado de um recurso do Azure que você precisa criar. Há dois recursos:

* Utilize o recurso **de autoria** para a formação para criar, editar, treinar e publicar.
* Utilize a **previsão** para o tempo de execução para enviar o texto do utilizador e receber uma previsão.

Conheça o ponto final da [previsão V3.](luis-migration-api-v3.md)

Utilize o código de [amostra dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-quickstart-code) para aprender e utilizar as tarefas mais comuns.

### <a name="rest-apis"></a>APIs REST

As APIS de ponto de extremidade de criação e de previsão estão disponíveis em APIs REST:

|Tipo|Versão|
|--|--|
|Criação|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[pré-visualização V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|predição|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Idioma |Documentação de referência|Pacote|Amostras|Guias de Início Rápido|
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
* [Bot Framework emulador](https://github.com/Microsoft/BotFramework-Emulator/releases) - uma aplicação de ambiente de trabalho que permite aos desenvolvedores de bots testar e depurar bots construídos usando o Bot Framework SDK


## <a name="next-steps"></a>Passos seguintes

* Conheça os [códigos](luis-reference-response-codes.md) de erro http comuns
* [Documentação de referência](https://docs.microsoft.com/azure/index#pivot=sdkstools) para todas as APIs e SDKs
* [Quadro bot](https://github.com/Microsoft/botbuilder-dotnet) e [Serviço Bot Azure](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Recipientes Cognitivos](../cognitive-services-container-support.md)
