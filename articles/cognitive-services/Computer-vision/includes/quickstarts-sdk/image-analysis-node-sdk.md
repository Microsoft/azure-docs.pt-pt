---
title: 'Quickstart: Biblioteca de clientes de Análise de Imagem para Node.js'
description: Começar com a biblioteca de clientes image Analysis para Node.js com este arranque rápido
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: d38b1f20547056c30f19dbbc77589643be1663a4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073652"
---
<a name="HOLTop"></a>

Utilize a biblioteca do cliente de Análise de Imagem para analisar uma imagem para tags, descrição de texto, rostos, conteúdo adulto e muito mais.

[Documentação de referência](/javascript/api/@azure/cognitiveservices-computervision/)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual de [Node.js](https://nodejs.org/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela.

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Instale o `ms-rest-azure` pacote e `@azure/cognitiveservices-computervision` NPM:

```console
npm install @azure/cognitiveservices-computervision
```

Também instale o módulo async:

```console
npm install async
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

Crie um novo ficheiro, *index.js,* e abra-o num editor de texto. Adicione as seguintes declarações de importação.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)que contém os exemplos de código neste arranque rápido.

Crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso de Visão De Computador que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da Análise de Imagem Node.js SDK.

|Nome|Descrição|
|---|---|
| [ComputadorVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador. Você instantanea-o com as suas informações de subscrição, e você usá-lo para fazer a maioria das operações de imagem.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação de Análise padrão. Especifica um conjunto de **valores VisualFeatureTypes** dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente de Análise de Imagem para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticar o cliente


Instantiar um cliente com o seu ponto final e chave. Crie um objeto [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) com a sua chave e ponto final e use-o para criar um objeto [ComputerVisionClient.](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Em seguida, defina uma `computerVision` função e declare uma série de async com função primária e função de retorno. Irá adicionar o seu código de arranque rápido na função principal e `computerVision` ligará na parte inferior do script. O resto do código neste arranque rápido entra na `computerVision` função.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#analyze-an-image) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analisar uma imagem

O código nesta secção analisa imagens remotas para extrair várias funcionalidades visuais. Pode fazer estas operações como parte do método **de análiseImage** do objeto do cliente, ou pode chamá-las usando métodos individuais. Consulte a [documentação de referência](/javascript/api/@azure/cognitiveservices-computervision/) para mais detalhes.

> [!NOTE]
> Também pode analisar uma imagem local. Consulte os métodos [ComputerVisionClient,](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) tais como **analisarImageInStream**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para cenários que envolvam imagens locais.

### <a name="get-image-description"></a>Obtenha a descrição da imagem

O seguinte código obtém a lista de legendas geradas para a imagem. Consulte [as imagens](../../concept-describing-images.md) para mais detalhes.

Primeiro, defina o URL de uma imagem para analisar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o seguinte código para obter a descrição da imagem e imprimi-la na consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O seguinte código obtém a categoria detetada da imagem. Consulte [as imagens categorize](../../concept-categorizing-images.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Defina a função de `formatCategories` ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O seguinte código obtém o conjunto de tags detetadas na imagem. Consulte [as etiquetas de conteúdo](../../concept-tagging-images.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Defina a função de `formatTags` ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detetar objetos

O código a seguir deteta objetos comuns na imagem e imprime-os na consola. Consulte [a deteção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Defina a função de ajudante `formatRectObjects` para retornar as coordenadas superior, esquerda, inferior e direita, juntamente com a largura e altura.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detetar marcas

O código que se segue deteta marcas e logótipos corporativos na imagem e imprime-os na consola. Consulte [a deteção da marca](../../concept-brand-detection.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve as faces detetadas na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Consulte [a deteção do rosto](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Defina a função de `formatRectFaces` ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código que se segue imprime a presença detetada de conteúdo adulto na imagem. Consulte [o conteúdo adulto, picante e sangrento](../../concept-detecting-adult-content.md) para mais detalhes.

Defina o URL da imagem para utilizar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Em seguida, adicione o seguinte código para detetar o conteúdo adulto e imprimir os resultados na consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cores de imagem

O código seguinte imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do acento. Consulte [os esquemas de cores](../../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a função de ajudante `printColorScheme` para imprimir os detalhes do esquema de cores para a consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico de domínio

A Image Analysis pode usar um modelo especializado para fazer mais análises nas imagens. Consulte [o conteúdo específico do domínio](../../concept-detecting-domain-content.md) para obter mais detalhes.

Primeiro, defina o URL de uma imagem para analisar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

O código seguinte analisa dados sobre marcos detetados na imagem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Defina a função de ajudante `formatRectDomain` para analisar os dados de localização sobre marcos detetados.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obtenha o tipo de imagem

O código que se segue imprime informações sobre o tipo de &mdash; imagem, quer se trate de clip art ou desenho de linha.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Defina a função de `describeType` ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Analisei uma imagem](?success=analyze-image#run-the-application) [que encontrei num problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)



## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Corri o requerimento](?success=run-the-application#clean-up-resources) [que encontrei.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a instalar a biblioteca de clientes de Análise de Imagem e a fazer chamadas básicas de análise de imagem. Em seguida, saiba mais sobre as funcionalidades da API de análise.

> [!div class="nextstepaction"]
>[Ligue para a API de Análise](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Visão geral da análise de imagem](../../overview-image-analysis.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).

