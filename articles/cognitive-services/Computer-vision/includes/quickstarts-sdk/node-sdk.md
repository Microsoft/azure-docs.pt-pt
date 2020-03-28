---
title: 'Quickstart: Biblioteca de clientes Computer Vision para Node.js'
description: Comece com a biblioteca de clientes Computer Vision para Node.js com este quickstart
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 234e2402287fd76bdfd96cb6884c3fd20e805d06
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136022"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Pacote de código fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | da biblioteca[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-computer-vision-azure-resource"></a>Criar um recurso Computer Vision Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para a Visão Computacional utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT` respectivamente.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela.

```console
mkdir myapp && cd myapp
```

Executar `npm init` o comando para criar uma `package.json` aplicação de nó com um ficheiro.

```console
npm init
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Instale `ms-rest-azure` `@azure/cognitiveservices-computervision` os pacotes e npm:

```console
npm install @azure/cognitiveservices-computervision
```

O ficheiro `package.json` da sua aplicação será atualizado com as dependências.

### <a name="prepare-the-nodejs-script"></a>Prepare o roteiro Node.js

Crie um novo ficheiro, *index.js,* e abra-o num editor de texto. Adicione as seguintes declarações de importação.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Em seguida, `computerVision` defina uma função e declare uma série de assinos com função primária e função de chamada. Você adicionará o seu código de arranque `computerVision` rápido na função primária e ligue para a parte inferior do script.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision Node.js SDK.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Esta aula é necessária para todas as funcionalidades de Visão Computacional. Você instantaneamente com a sua informação de subscrição, e você usa-o para fazer a maioria das operações de imagem.|
|[Tipos de Características Visuais](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação padrão da Análise. Especifica um conjunto de valores **VisualFeatureTypes** dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Computer Vision para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie variáveis para o ponto final e chave azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Instantifique um cliente com o seu ponto final e chave. Crie um objeto [ApiKeyCredencia](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) com a sua chave e ponto final e use-o para criar um objeto [ComputerVisionClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código nesta secção analisa imagens remotas para extrair várias características visuais. Pode fazer estas operações como parte do método **de análiseImagem** do objeto cliente, ou pode chamá-las usando métodos individuais. Consulte a [documentação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) de referência para mais detalhes.

> [!NOTE]
> Também pode analisar uma imagem local. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para cenários que envolvam imagens locais.

### <a name="get-image-description"></a>Obtenha descrição da imagem

O código seguinte obtém a lista de legendas geradas para a imagem. Consulte [imagens de descrever](../../concept-describing-images.md) para mais detalhes.

Primeiro, defina o URL de uma imagem para analisar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o seguinte código para obter a descrição da imagem e imprima-o na consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O código seguinte obtém a categoria detetada da imagem. Consulte [as imagens do Categorize](../../concept-categorizing-images.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definir a função `formatCategories`de ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O código seguinte obtém o conjunto de etiquetas detetadas na imagem. Consulte [as etiquetas de Conteúdo](../../concept-tagging-images.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definir a função `formatTags`de ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detetar objetos

O código seguinte deteta objetos comuns na imagem e imprime-os na consola. Consulte a [deteção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definir a função `formatRectObjects`de ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detetar marcas

O código seguinte deteta marcas e logótipos corporativos na imagem e imprime-os para a consola. Consulte a [deteção da marca](../../concept-brand-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve os rostos detetados na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Consulte a [deteção do rosto](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definir a função `formatRectFaces`de ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código seguinte imprime a presença detetada de conteúdo adulto na imagem. Consulte [conteúdo adulto, picante e sangrento](../../concept-detecting-adult-content.md) para mais detalhes.

Defina o URL da imagem a utilizar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Em seguida, adicione o seguinte código para detetar conteúdo adulto e imprimir os resultados na consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cor de imagem

O código que se segue imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do sotaque. Consulte [esquemas de cores](../../concept-detecting-color-schemes.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a `printColorScheme` função auxiliar para imprimir os detalhes do esquema de cores para a consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico do domínio

A Computer Vision pode usar um modelo especializado para fazer uma análise mais aprofundada das imagens. Consulte [conteúdo específico do Domínio](../../concept-detecting-domain-content.md) para obter mais detalhes.

Primeiro, defina o URL de uma imagem para analisar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

O código que se segue analisa os dados sobre marcos detetados na imagem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Defina a `formatRectDomain` função de ajudante para analisar os dados de localização sobre marcos detetados.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código que se segue imprime informações sobre o tipo de imagem,&mdash;quer se trate de clip art ou de desenho de linha.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definir a função `describeType`de ajudante:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computorizada pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres.

> [!NOTE]
> Pode também ler texto a partir de uma imagem local. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para cenários que envolvam imagens locais.

### <a name="set-up-test-images"></a>Configurar imagens de teste

Guarde uma referência do URL das imagens das quais pretende extrair texto.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Ligue para a API Recognise

Adicione o código abaixo, `recognizeText` que chama a função para as imagens dadas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Defina `recognizeText` a função. Isto chama o método **de reconhecimentoTexto** no objeto do cliente, que devolve um ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem. Em seguida, utiliza o ID de operação para verificar a operação em intervalos de um segundo até que os resultados sejam devolvidos. Em seguida, devolve os resultados extraídos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Em seguida, defina `printRecText`a função auxiliar , que imprime os resultados de uma operação Recognise para a consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o `node` pedido com o comando no seu ficheiro de arranque rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Referência da API da Visão Computacional (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [O que é a Imagem Digitalizada?](../../Home.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
