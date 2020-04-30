---
title: Biblioteca de clientes Moderador de Conteúdo Python quickstart
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprenda a começar com a biblioteca de clientes do Moderador de Conteúdo de Serviços Cognitivos Azure para python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 8a925aab3fb864643a873433b6df4f2275cae365
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187365"
---
Começar com a biblioteca de clientes moderador de conteúdo para python. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Content Moderator é um serviço cognitivo que verifica o conteúdo de texto, imagem e vídeo para material potencialmente ofensivo, arriscado ou de outra forma indesejável. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores.

Utilize a biblioteca de clientes Moderador de Conteúdo para Python para:

* [Texto moderado](#moderate-text)
* [Use uma lista de termos personalizados](#use-a-custom-terms-list)
* [Imagens moderadas](#moderate-images)
* [Use uma lista de imagens personalizada](#use-a-custom-image-list)
* [Criar uma revisão](#create-a-review)

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | da biblioteca[(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Samples](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-content-moderator-azure-resource"></a>Criar um recurso Azure moderador de conteúdo

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para moderador de conteúdo utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e, `CONTENT_MODERATOR_ENDPOINT`respectivamente.
 
### <a name="create-a-python-script"></a>Criar um roteiro de pitão

Crie um novo guião Python e abra-o no seu editor preferido ou IDE. Em seguida, `import` adicione as seguintes declarações ao topo do ficheiro.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para a localização final do seu recurso e chave como variáveis ambientais. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Se criou as variáveis ambientais depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder às variáveis.

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Pode instalar a biblioteca de clientes Moderador de Conteúdo com o seguinte comando:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Moderador de Conteúdo Python.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Esta aula é necessária para todas as funcionalidades moderadoras de conteúdo. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Esta aula fornece a funcionalidade para analisar imagens para conteúdos para adultos, informações pessoais ou rostos humanos.|
|[Mensagens ModeraçãoOperações](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Esta aula fornece a funcionalidade para analisar texto para linguagem, profanação, erros e informações pessoais.|
[ComentáriosOperações](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Moderador de Conteúdo para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Texto moderado](#moderate-text)
* [Use uma lista de termos personalizados](#use-a-custom-terms-list)
* [Imagens moderadas](#moderate-images)
* [Use uma lista de imagens personalizada](#use-a-custom-image-list)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou variáveis ambientais](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave moderadora de conteúdo e ponto final.

Instantifique um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredenciais](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o seu ponto final para criar um objeto [ContentModeratorClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Texto moderado

O código seguinte utiliza um cliente moderador de conteúdo para analisar um corpo de texto e imprimir os resultados para a consola. Em primeiro lugar, crie uma **text_files/pasta** na raiz do seu projeto e adicione um ficheiro *content_moderator_text_moderation.txt.* Adicione o seu próprio texto a este ficheiro ou utilize o seguinte texto da amostra:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Adicione uma referência à nova pasta.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Em seguida, adicione o seguinte código ao seu script Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Use uma lista de termos personalizados

O código seguinte mostra como gerir uma lista de termos personalizados para moderação de texto. Pode utilizar a classe [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) para criar uma lista de termos, gerir os termos individuais e rastrear outros corpos de texto contra ele.

### <a name="get-sample-text"></a>Obtenha texto de amostra

Para utilizar esta amostra, tem de criar uma **text_files/pasta** na raiz do seu projeto e adicionar um ficheiro *content_moderator_term_list.txt.* Este ficheiro deve conter texto orgânico que será verificado contra a lista de termos. Pode utilizar o seguinte texto da amostra:

```
This text contains the terms "term1" and "term2".
```

Adicione uma referência à pasta se ainda não tiver definido uma.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Criar uma lista

Adicione o seguinte código ao seu script Python para criar uma lista de termos personalizados e guardar o seu valor de ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definir detalhes da lista

Pode usar o ID de uma lista para editar o seu nome e descrição.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Adicione um termo à lista

O código seguinte `"term1"` adiciona `"term2"` os termos e a lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Obter todos os termos na lista

Pode usar o ID da lista para devolver todos os termos da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Atualizar o índice da lista

Sempre que adicionar ou remover os termos da lista, tem de atualizar o índice antes de poder utilizar a lista atualizada.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Texto de tela contra a lista

A principal funcionalidade da lista de termos personalizados é comparar um corpo de texto com a lista e descobrir se existem termos correspondentes. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Remover um termo de uma lista

O código seguinte remove `"term1"` o termo da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Remova todos os termos de uma lista

Utilize o seguinte código para limpar uma lista de todos os seus termos.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Excluir lista

Utilize o seguinte código para eliminar uma lista de termos personalizados.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Imagens moderadas

O código seguinte utiliza um cliente Moderador de Conteúdo, juntamente com um objeto [ImageModerationOperations,](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) para analisar imagens para conteúdo adulto e picante.

### <a name="get-images"></a>Obter imagens

Defina uma referência a algumas imagens para analisar.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Em seguida, adicione o seguinte código ao iterato através das suas imagens. O resto do código nesta secção entrará neste ciclo.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verifique se há conteúdo adulto/picante

O código seguinte verifica a imagem no URL dado para conteúdo adulto ou picante e imprime resultados para a consola. Consulte o guia de conceitos de [moderação da Imagem](../../image-moderation-api.md) para obter informações sobre o que estes termos significam.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Verifique se há texto visível

O código seguinte verifica a imagem para obter conteúdo de texto visível e os resultados das impressões para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verifique se há rostos

O código seguinte verifica a imagem para rostos humanos e os resultados das impressões para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Use uma lista de imagens personalizada

O código que se segue mostra como gerir uma lista personalizada de imagens para moderação de imagem. Esta funcionalidade é útil se a sua plataforma receber frequentemente casos do mesmo conjunto de imagens que pretende ser exibido. Ao manter uma lista destas imagens específicas, pode melhorar o desempenho. A classe [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) permite-lhe criar uma lista de imagens, gerir as imagens individuais da lista e comparar outras imagens com ela.

Crie as seguintes variáveis de texto para armazenar os URLs de imagem que utilizará neste cenário.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Esta não é a lista adequada em si, mas uma `add images` lista informal de imagens que serão adicionadas na secção do código.


### <a name="create-an-image-list"></a>Criar uma lista de imagens

Adicione o seguinte código para criar uma lista de imagens e guarde uma referência ao seu ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Adicione imagens a uma lista

O código seguinte adiciona todas as suas imagens à lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Defina a **função add_images** ajudante em outro lugar do seu script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Obtenha imagens na lista

O código que se segue imprime os nomes de todas as imagens da sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Detalhes da lista de atualizações

Pode utilizar o ID da lista para atualizar o nome e a descrição da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Obtenha detalhes da lista

Utilize o seguinte código para imprimir os dados atuais da sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Atualizar o índice da lista

Depois de adicionar ou remover imagens, tem de atualizar o índice da lista antes de o poder utilizar para rastrear outras imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Combine imagens com a lista

A principal função das listas de imagens é comparar novas imagens e ver se existem alguma correspondência.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Remova uma imagem da lista

O código que se segue remove um item da lista. Neste caso, é uma imagem que não corresponde à categoria de lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Remova todas as imagens de uma lista

Utilize o seguinte código para limpar uma lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Eliminar a lista de imagens

Utilize o seguinte código para eliminar uma determinada lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Criar uma revisão

Pode utilizar a biblioteca de clientes Do Moderador de Conteúdo Python para alimentar conteúdos na [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam revê-lo. Para saber mais sobre a ferramenta Review, consulte o guia conceptual da [ferramenta Rever](../../review-tool-user-guide/human-in-the-loop.md).

O código seguinte utiliza a classe [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) para criar uma revisão, recuperar o seu ID e verificar os seus dados depois de receber a entrada humana através do portal web da ferramenta Review.

### <a name="get-review-credentials"></a>Obter credenciais de revisão

Primeiro, inscreva-se na ferramenta Review e recupere o nome da sua equipa. Em seguida, atribua-o à variável apropriada no código. Opcionalmente, pode configurar um ponto final de callback para receber atualizações sobre a atividade da revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Criar uma revisão de imagem

Adicione o seguinte código para criar e publique uma revisão para o URL de imagem dada. O código guarda uma referência ao ID de revisão. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Obtenha detalhes da revisão

Utilize o seguinte código para verificar os detalhes de uma determinada revisão. Depois de criar a revisão, pode ir à ferramenta 'Rever' e interagir com o conteúdo. Para obter informações sobre como [fazê-lo,](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)consulte o guia de comentários . Quando terminar, pode executar este código novamente, e ele vai recuperar os resultados do processo de revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Se usou um ponto final de chamada neste cenário, deve receber um evento neste formato:

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

Execute o `python` pedido com o comando no seu ficheiro de arranque rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca De Conteúdo Moderador Python para fazer tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação através da leitura de um guia conceptual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Content Moderator do Azure?](../../overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).