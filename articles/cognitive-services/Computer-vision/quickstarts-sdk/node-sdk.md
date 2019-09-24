---
title: 'Início rápido: Biblioteca de cliente Pesquisa Visual Computacional para node. js | Microsoft Docs'
description: Introdução à biblioteca de cliente Pesquisa Visual Computacional para node. js...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/18/2019
ms.author: pafarley
ms.openlocfilehash: bcce9c53e2618f9501dde63905fb834bce7985ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204507"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Início rápido: Biblioteca de cliente Pesquisa Visual Computacional para node. js

Introdução à biblioteca de cliente Pesquisa Visual Computacional para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. 

Use a biblioteca de cliente Pesquisa Visual Computacional para node. js para:

* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

[](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) [de NPM (pacote de](https://www.npmjs.com/package/azure-cognitiveservices-computervision) | [código](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | -fonte de biblioteca de documentação de referência)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um Pesquisa Visual Computacional recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Visual Computacional usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL `COMPUTER_VISION_SUBSCRIPTION_KEY` do ponto de extremidade, nomeadas e `COMPUTER_VISION_ENDPOINT` respectivamente.
 
### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o `npm init` comando para criar um aplicativo de nó com `package.json` um arquivo. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os `ms-rest-azure` pacotes `azure-cognitiveservices-computervision` e NPM:

```console
npm install azure-cognitiveservices-computervision ms-rest-azure
```

O arquivo do `package.json` aplicativo será atualizado com as dependências.

### <a name="prepare-the-nodejs-script"></a>Preparar o script node. js

Crie um novo arquivo, *index. js*, e abra-o em um editor de texto. Adicione as seguintes instruções de importação.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Em seguida, defina uma `computerVision` função e declare uma série assíncrona com a função principal e a função de retorno de chamada. Você adicionará o código de início rápido à função principal e chamará `computerVision` na parte inferior do script.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK Pesquisa Visual Computacional node. js.

|Name|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para fazer a maioria das operações de imagem.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitas em uma operação de análise padrão. Você especifica um conjunto de valores de **VisualFeatureTypes** dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Pesquisa Visual Computacional para node. js:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) com sua chave e ponto de extremidade e use-o para criar um objeto [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código nesta seção analisa as imagens remotas para extrair vários recursos visuais. Você pode fazer essas operações como parte do método **analyzeImage** do objeto cliente, ou pode chamá-las usando métodos individuais. Consulte a [documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) para obter detalhes.

> [!NOTE]
> Você também pode analisar uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para ver cenários que envolvem imagens locais.

### <a name="get-image-description"></a>Obter descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Consulte [descrever imagens](../concept-describing-images.md) para obter mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o código a seguir para obter a descrição da imagem e imprimi-la no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obter categoria de imagem

O código a seguir obtém a categoria detectada da imagem. Consulte [categorizar imagens](../concept-categorizing-images.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Defina a função `formatCategories`auxiliar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obter marcas de imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Consulte [marcas de conteúdo](../concept-tagging-images.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Defina a função `formatTags`auxiliar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detectar objetos

O código a seguir detecta objetos comuns na imagem e os imprime no console do. Consulte [detecção de objeto](../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Defina a função `formatRectObjects`auxiliar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta marcas corporativas e logotipos na imagem e os imprime no console. Consulte [detecção de marca](../concept-brand-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código a seguir retorna as faces detectadas na imagem com suas coordenadas de retângulo e seleciona atributos de face. Consulte [detecção facial](../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Defina a função `formatRectFaces`auxiliar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-or-racy-content"></a>Detectar conteúdo adulto ou erótico

O código a seguir imprime a presença detectada de conteúdo adulto ou erótico na imagem. Consulte [conteúdo adulto e erótico](../concept-detecting-adult-content.md) para obter mais detalhes.

Defina a URL da imagem a ser usada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)] 

Em seguida, adicione o código a seguir para detectar conteúdo adulto e imprimir os resultados no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)] 

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Consulte [esquemas de cores](../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a função `printColorScheme` auxiliar para imprimir os detalhes do esquema de cores no console do.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

Pesquisa Visual Computacional pode usar um modelo especializado para fazer mais análises sobre imagens. Consulte [conteúdo específico do domínio](../concept-detecting-domain-content.md) para obter mais detalhes. 

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

O código a seguir analisa dados sobre os pontos de referência detectados na imagem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Defina a função `formatRectDomain` auxiliar para analisar os dados de localização sobre os pontos de referência detectados.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código a seguir imprime informações sobre o tipo&mdash;de imagem, seja Clip-Art ou desenho de linha.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Defina a função `describeType`auxiliar:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

Pesquisa Visual Computacional pode ler o texto visível em uma imagem e convertê-lo em um fluxo de caracteres.

> [!NOTE]
> Você também pode ler texto de uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para ver cenários que envolvem imagens locais.

### <a name="set-up-test-images"></a>Configurar imagens de teste

Salve uma referência à URL das imagens das quais você deseja extrair o texto.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Chamar a API de reconhecimento

Adicione o código abaixo, que chama a `recognizeText` função para as imagens fornecidas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Defina a `recognizeText` função. Isso chama o método **recognizeText** no objeto cliente, que retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem. Em seguida, ele usa a ID da operação para verificar a operação em intervalos de um segundo até que os resultados sejam retornados. Em seguida, ele retorna os resultados extraídos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Em seguida, defina a função `printRecText`auxiliar, que imprime os resultados de uma operação de reconhecimento para o console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o `node` comando no seu arquivo de início rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Referência de API da Pesquisa Visual Computacional (Node. js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [O que é o API da Pesquisa Visual Computacional?](../Home.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).