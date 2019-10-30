---
title: 'Início rápido: biblioteca de cliente Content Moderator para Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente Content Moderator para Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 28c70e7d226e8f4b2458234e1f14754cdbb1825c
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044039"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Início rápido: biblioteca de cliente Content Moderator para Python

Introdução à biblioteca de cliente Content Moderator para Python. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Content Moderator é um serviço cognitiva que verifica o conteúdo de texto, imagem e vídeo para materiais potencialmente ofensivos, arriscados ou indesejáveis. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores.

Use a biblioteca de cliente Content Moderator para Python para:

* [Texto moderado](#moderate-text)
* [Usar uma lista de termos personalizados](#use-a-custom-terms-list)
* [Imagens moderadas](#moderate-images)
* [Usar uma lista de imagens Personalizada](#use-a-custom-image-list)
* [Criar uma revisão](#create-a-review)

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python)  |   | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/)  | [amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) do[código-fonte de biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-content-moderator-azure-resource"></a>Criar um Content Moderator recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Content Moderator usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir seu recurso no [portal do Azure](https://portal.azure.com/)

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL do ponto de extremidade, denominada `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e `CONTENT_MODERATOR_ENDPOINT`, respectivamente.
 
### <a name="create-a-python-script"></a>Criar um script Python

Crie um novo script Python e abra-o em seu editor ou IDE preferido. Em seguida, adicione as instruções de `import` a seguir à parte superior do arquivo.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para o local e a chave do ponto de extremidade do recurso como variáveis de ambiente. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Se você criou as variáveis de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar as variáveis.

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Você pode instalar a biblioteca de cliente do Content Moderator com o seguinte comando:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos do SDK do Content Moderator Python.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Essa classe é necessária para todas as funcionalidades de Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo adulto, informações pessoais ou faces humanas.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Essa classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Content Moderator para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Texto moderado](#moderate-text)
* [Usar uma lista de termos personalizados](#use-a-custom-terms-list)
* [Imagens moderadas](#moderate-images)
* [Usar uma lista de imagens Personalizada](#use-a-custom-image-list)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou variáveis de ambiente](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para sua chave de Content moderator e ponto de extremidade.

Crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com sua chave e use-o com seu ponto de extremidade para criar um objeto [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Texto moderado

O código a seguir usa um cliente Content Moderator para analisar um corpo de texto e imprimir os resultados no console. Primeiro, crie um **text_files/** pasta na raiz do seu projeto e adicione um arquivo *content_moderator_text_moderation. txt* . Adicione seu próprio texto a este arquivo ou use o seguinte texto de exemplo:

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

Adicione uma referência à nova pasta.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Em seguida, adicione o código a seguir ao seu script Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Usar uma lista de termos personalizados

O código a seguir mostra como gerenciar uma lista de termos personalizados para moderação de texto. Você pode usar a classe [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) para criar uma lista de termos, gerenciar os termos individuais e a tela de outros corpos de texto em relação a ele.

### <a name="get-sample-text"></a>Obter texto de exemplo

Para usar este exemplo, você deve criar um **text_files/** pasta na raiz do seu projeto e adicionar um arquivo *content_moderator_term_list. txt* . Esse arquivo deve conter um texto orgânica que será verificado em relação à lista de termos. Você pode usar o seguinte texto de exemplo:

```
This text contains the terms "term1" and "term2".
```

Adicione uma referência à pasta se você ainda não tiver definido uma.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Criar uma lista

Adicione o código a seguir ao seu script Python para criar uma lista de termos personalizados e salvar seu valor de ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definir detalhes da lista

Você pode usar a ID da lista para editar seu nome e descrição.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Adicionar um termo à lista

O código a seguir adiciona os termos `"term1"` e `"term2"` à lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Obter todos os termos na lista

Você pode usar a ID da lista para retornar todos os termos da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Atualizar o índice da lista

Sempre que você adicionar ou remover os termos da lista, deverá atualizar o índice antes de poder usar a lista atualizada.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Texto da tela na lista

A principal funcionalidade da lista de termos personalizados é comparar um corpo de texto com a lista e descobrir se há termos correspondentes. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Remover um termo de uma lista

O código a seguir remove o termo `"term1"` da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Remover todos os termos de uma lista

Use o código a seguir para limpar uma lista de todos os seus termos.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Excluir lista

Use o código a seguir para excluir uma lista de termos personalizados.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Imagens moderadas

O código a seguir usa um cliente Content Moderator, juntamente com um objeto [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) , para analisar imagens para conteúdo adulto e erótico.

### <a name="get-images"></a>Obter imagens

Defina uma referência a algumas imagens para analisar.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Em seguida, adicione o código a seguir para iterar pelas imagens. O restante do código nesta seção entrará dentro desse loop.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificar conteúdo adulto/erótico

O código a seguir verifica a imagem na URL fornecida para conteúdo adulto ou erótico e imprime os resultados no console. Consulte o guia de [conceitos de moderação de imagem](./image-moderation-api.md) para obter informações sobre o que esses termos significam.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Verificar texto visível

O código a seguir verifica a imagem em busca de conteúdo de texto visível e imprime os resultados no console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificar se há faces

O código a seguir verifica a imagem de faces humanas e imprime os resultados no console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Usar uma lista de imagens Personalizada

O código a seguir mostra como gerenciar uma lista personalizada de imagens para moderação da imagem. Esse recurso será útil se a plataforma receber com frequência instâncias do mesmo conjunto de imagens que você deseja desconectar. Ao manter uma lista dessas imagens específicas, você pode melhorar o desempenho. A classe [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) permite que você crie uma lista de imagens, gerencie as imagens individuais na lista e compare outras imagens com ela.

Crie as variáveis de texto a seguir para armazenar as URLs de imagem que você usará nesse cenário.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Essa não é a própria lista apropriada, mas uma lista informal de imagens que será adicionada na seção `add images` do código.


### <a name="create-an-image-list"></a>Criar uma lista de imagens

Adicione o código a seguir para criar uma lista de imagens e salvar uma referência à sua ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Adicionar imagens a uma lista

O código a seguir adiciona todas as suas imagens à lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Defina a função auxiliar **add_images** em outro lugar em seu script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Obter imagens na lista

O código a seguir imprime os nomes de todas as imagens em sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Atualizar detalhes da lista

Você pode usar a ID da lista para atualizar o nome e a descrição da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Obter detalhes da lista

Use o código a seguir para imprimir os detalhes atuais da sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Atualizar o índice da lista

Depois de adicionar ou remover imagens, você deve atualizar o índice da lista antes de poder usá-lo para fazer a tela de outras imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Corresponder imagens à lista

A função principal das listas de imagens é comparar novas imagens e ver se há quaisquer correspondências.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Remover uma imagem da lista

O código a seguir remove um item da lista. Nesse caso, é uma imagem que não corresponde à categoria da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Remover todas as imagens de uma lista

Use o código a seguir para limpar uma lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Excluir a lista de imagens

Use o código a seguir para excluir uma determinada lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Criar uma revisão

Você pode usar o SDK do Python Content Moderator para alimentar conteúdo na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam examiná-lo. Para saber mais sobre a ferramenta de revisão, consulte o [guia conceitual da ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md).

O código a seguir usa a classe [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) para criar uma revisão, recuperar sua ID e verificar seus detalhes depois de receber a entrada humana por meio do portal da Web da ferramenta de revisão.

### <a name="get-review-credentials"></a>Obter credenciais de revisão

Primeiro, entre na ferramenta de revisão e recupere o nome da equipe. Em seguida, atribua-o à variável apropriada no código. Opcionalmente, você pode configurar um ponto de extremidade de retorno de chamada para receber atualizações sobre a atividade da revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Criar uma revisão de imagem

Adicione o código a seguir para criar e lançar uma revisão para a URL da imagem fornecida. O código salva uma referência à ID de revisão. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Obter detalhes da revisão

Use o código a seguir para verificar os detalhes de uma determinada revisão. Depois de criar a revisão, você pode ir para a ferramenta de revisão por conta própria e interagir com o conteúdo. Para obter informações sobre como fazer isso, consulte o [Guia de instruções de análise](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Quando tiver terminado, você poderá executar esse código novamente e ele recuperará os resultados do processo de revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Se você usou um ponto de extremidade de retorno de chamada nesse cenário, ele deverá receber um evento neste formato:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o comando `python` em seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar a biblioteca Content Moderator Python para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Azure Content Moderator?](./overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).