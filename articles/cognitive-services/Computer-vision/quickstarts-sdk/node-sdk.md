---
title: 'Início rápido: biblioteca de cliente Pesquisa Visual Computacional para node. js'
description: Introdução à biblioteca de cliente do Pesquisa Visual Computacional para node. js com este guia de início rápido
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 530665168cc1ed21f1664e3012bbf37e52d96fbf
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716163"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Início rápido: biblioteca de cliente Pesquisa Visual Computacional para node. js

Introdução à biblioteca de cliente Pesquisa Visual Computacional para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente Pesquisa Visual Computacional para node. js para:

* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Código fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | Pacote de  [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um Pesquisa Visual Computacional recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para a Visão Computacional utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT` respectivamente.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar uma aplicação de nó com um ficheiro `package.json`.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os pacotes `ms-rest-azure` e `@azure/cognitiveservices-computervision` NPM:

```console
npm install @azure/cognitiveservices-computervision
```

O ficheiro `package.json` da sua aplicação será atualizado com as dependências.

### <a name="prepare-the-nodejs-script"></a>Preparar o script node. js

Crie um novo ficheiro, *index.js,* e abra-o num editor de texto. Adicione as seguintes instruções de importação.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Em seguida, defina uma função `computerVision` e declare uma série de asincronização com função primária e função de chamada. Você adicionará o seu código de arranque rápido na função primária e chamará `computerVision` na parte inferior do script.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK Pesquisa Visual Computacional node. js.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para fazer a maioria das operações de imagem.|
|[Tipos de Características Visuais](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitas em uma operação de análise padrão. Especifica um conjunto de valores **VisualFeatureTypes** dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Pesquisa Visual Computacional para node. js:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [ApiKeyCredencia](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) com a sua chave e ponto final e use-o para criar um objeto [ComputerVisionClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código nesta seção analisa as imagens remotas para extrair vários recursos visuais. Pode fazer estas operações como parte do método **de análiseImagem** do objeto cliente, ou pode chamá-las usando métodos individuais. Consulte a [documentação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) de referência para mais detalhes.

> [!NOTE]
> Você também pode analisar uma imagem local. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para cenários que envolvam imagens locais.

### <a name="get-image-description"></a>Obter descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Consulte [imagens de descrever](../concept-describing-images.md) para mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o código a seguir para obter a descrição da imagem e imprimi-la no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obter categoria de imagem

O código a seguir obtém a categoria detectada da imagem. Consulte [as imagens do Categorize](../concept-categorizing-images.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Defina a função de ajudante `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obter marcas de imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Consulte [as etiquetas de Conteúdo](../concept-tagging-images.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Defina a função de ajudante `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detectar objetos

O código a seguir detecta objetos comuns na imagem e os imprime no console do. Consulte a [deteção de objetos](../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Defina a função de ajudante `formatRectObjects`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta marcas corporativas e logotipos na imagem e os imprime no console. Consulte a [deteção da marca](../concept-brand-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código a seguir retorna as faces detectadas na imagem com suas coordenadas de retângulo e seleciona atributos de face. Consulte a [deteção do rosto](../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Defina a função de ajudante `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo adulto, erótico ou terríveis

O código a seguir imprime a presença detectada de conteúdo adulto na imagem. Consulte [conteúdo adulto, picante e sangrento](../concept-detecting-adult-content.md) para mais detalhes.

Defina a URL da imagem a ser usada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Em seguida, adicione o código a seguir para detectar conteúdo adulto e imprimir os resultados no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Consulte [esquemas de cores](../concept-detecting-color-schemes.md) para mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a função de ajudante `printColorScheme` imprimir os detalhes do esquema de cores para a consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

Pesquisa Visual Computacional pode usar um modelo especializado para fazer mais análises sobre imagens. Consulte [conteúdo específico do Domínio](../concept-detecting-domain-content.md) para obter mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

O código a seguir analisa dados sobre os pontos de referência detectados na imagem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Defina a função de ajudante `formatRectDomain` analisar os dados de localização sobre marcos detetados.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código que se segue imprime informações sobre o tipo de imagem&mdash;se é clip art ou desenho de linha.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Defina a função de ajudante `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

Pesquisa Visual Computacional pode ler o texto visível em uma imagem e convertê-lo em um fluxo de caracteres.

> [!NOTE]
> Você também pode ler texto de uma imagem local. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para cenários que envolvam imagens locais.

### <a name="set-up-test-images"></a>Configurar imagens de teste

Salve uma referência da URL das imagens das quais você deseja extrair o texto.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Chamar a API de reconhecimento

Adicione o código abaixo, que chama a função `recognizeText` para as imagens dadas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Defina a função `recognizeText`. Isto chama o método **de reconhecimentoTexto** no objeto do cliente, que devolve um ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem. Em seguida, ele usa a ID da operação para verificar a operação em intervalos de um segundo até que os resultados sejam retornados. Em seguida, ele retorna os resultados extraídos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Em seguida, defina a função do ajudante `printRecText`, que imprime os resultados de uma operação Recognise para a consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Executar a aplicação

Faça o pedido com o comando `node` no seu ficheiro de arranque rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Referência da API da Visão Computacional (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [O que é visão computacional?](../Home.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
