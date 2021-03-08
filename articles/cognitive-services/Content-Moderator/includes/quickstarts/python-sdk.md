---
title: Marketing Moderador Python biblioteca cliente quickstart
titleSuffix: Azure Cognitive Services
description: Neste quickstart, aprenda a começar com a biblioteca de clientes Azure Content Moderador para Python. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 07828e7faff61086ea982b8017bc3c590e386be1
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444439"
---
Começa com a biblioteca de clientes Azure Content Moderador para Python. Siga estes passos para instalar o pacote PiPy e experimente o código de exemplo para tarefas básicas. 

Content Moderator é um serviço de IA que permite lidar com conteúdos potencialmente ofensivos, arriscados ou indesejáveis. Utilize o serviço de moderação de conteúdo movido a IA para digitalizar texto, imagem e vídeos e aplicar automaticamente as bandeiras de conteúdo. Em seguida, integre a sua app com a ferramenta Review, um ambiente moderador on-line para uma equipa de revisores humanos. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.

Utilize a biblioteca de clientes content Moderador para Python para:

* Texto moderado
* Use uma lista de termos personalizados
* Imagens moderadas
* Use uma lista de imagens personalizada
* Criar uma análise

[Documentação de referência](/python/api/overview/azure/cognitiveservices/contentmoderator)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator)  |  [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A sua instalação Python deve incluir [pip](https://pip.pypa.io/en/stable/). Pode verificar se tem pip instalado em funcionamento `pip --version` na linha de comando. Obtenha pip instalando a versão mais recente de Python.
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" crie um recurso De Moderador de Conteúdo crie um recurso De Moderador de Conteúdo no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao Moderador de Conteúdo. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.


## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente Content Moderador com o seguinte comando:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo script Python e abra-o no seu editor preferido ou IDE. Em seguida, adicione as `import` seguintes declarações ao topo do ficheiro.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py)que contém os exemplos de código neste arranque rápido.

Em seguida, crie variáveis para a localização e chave do ponto final do seu recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso de Moderador de Conteúdo que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [cofre de chaves Azure](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Do Moderador de Conteúdo Python.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient)|Esta classe é necessária para todas as funcionalidades do Moderador de Conteúdo. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes.|
|[ImageModerationOperações](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations)|Esta classe fornece a funcionalidade de análise de imagens para conteúdo adulto, informações pessoais ou rostos humanos.|
|[TextModerationOperações](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations)|Esta classe fornece a funcionalidade de análise de texto para linguagem, profanação, erros e informações pessoais.|
[Comentários Operações](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e avaliações humanas.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes do Content Moderador para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Texto moderado](#moderate-text)
* [Use uma lista de termos personalizados](#use-a-custom-terms-list)
* [Imagens moderadas](#moderate-images)
* [Use uma lista de imagens personalizada](#use-a-custom-image-list)
* [Criar uma análise](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com a sua chave e use-o com o seu ponto final para criar um objeto [ContentModeratorClient.](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient)

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Texto moderado

O código que se segue utiliza um cliente Moderador de Conteúdo para analisar um conjunto de textos e imprimir os resultados na consola. Em primeiro lugar, crie uma **pasta de text_files/pasta** na raiz do seu projeto e adicione um ficheiro *content_moderator_text_moderation.txt.* Adicione o seu próprio texto a este ficheiro ou use o seguinte texto de amostra:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Adicione uma referência à nova pasta.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Em seguida, adicione o seguinte código ao seu script Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Use uma lista de termos personalizados

O código que se segue mostra como gerir uma lista de termos personalizados para moderação de texto. Pode utilizar a classe [ListManagementTermListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations) para criar uma lista de termos, gerir os termos individuais e rastrear outros corpos de texto contra ele.

### <a name="get-sample-text"></a>Obtenha texto de amostra

Para utilizar esta amostra, tem de criar uma **pasta de text_files/pasta** na raiz do seu projeto e adicionar um ficheiro *content_moderator_term_list.txt.* Este ficheiro deve conter texto orgânico que será verificado com a lista de termos. Pode utilizar o seguinte texto de amostra:

```
This text contains the terms "term1" and "term2".
```

Adicione uma referência à pasta se ainda não tiver definido uma.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Criar uma lista

Adicione o seguinte código ao seu script Python para criar uma lista de termos personalizados e guardar o seu valor de ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definir detalhes da lista

Pode utilizar o ID de uma lista para editar o seu nome e descrição.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Adicione um termo à lista

O seguinte código adiciona os termos `"term1"` e `"term2"` a lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Obtenha todos os termos da lista

Pode utilizar o ID da lista para devolver todos os termos da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Atualizar o índice de lista

Sempre que adicionar ou remover os termos da lista, tem de atualizar o índice antes de poder utilizar a lista atualizada.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Texto de tela contra a lista

A principal funcionalidade da lista de termos personalizados é comparar um conjunto de texto com a lista e descobrir se existem quaisquer termos correspondentes. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Remover um termo de uma lista

O seguinte código remove o termo `"term1"` da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Remova todos os termos de uma lista

Utilize o seguinte código para limpar uma lista de todos os seus termos.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>Excluir uma lista

Utilize o seguinte código para eliminar uma lista de termos personalizados.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Imagens moderadas

O código que se segue utiliza um cliente Moderador de Conteúdo, juntamente com um objeto [ImageModerationOperations,](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations) para analisar imagens para conteúdo adulto e picante.

### <a name="get-sample-images"></a>Obtenha imagens de amostra

Defina uma referência a algumas imagens para analisar.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Em seguida, adicione o seguinte código para iterar através das suas imagens. O resto do código nesta secção entrará neste ciclo.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verifique se há conteúdo adulto/picante

O código a seguir verifica a imagem no URL dado para conteúdo adulto ou picante e imprime resultados para a consola. Consulte o guia [de conceitos de moderação](../../image-moderation-api.md) de imagem para obter informações sobre o que estes termos significam.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Verifique se há texto visível

O código a seguir verifica a imagem para o conteúdo de texto visível e imprime os resultados para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verifique se há rostos

O código seguinte verifica a imagem para rostos humanos e imprime resultados para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Use uma lista de imagens personalizada

O código que se segue mostra como gerir uma lista personalizada de imagens para moderação de imagem. Esta funcionalidade é útil se a sua plataforma receber frequentemente casos do mesmo conjunto de imagens que pretende selecionar. Mantendo uma lista destas imagens específicas, pode melhorar o desempenho. A classe [ListManagementImageListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations) permite-lhe criar uma lista de imagens, gerir as imagens individuais da lista e comparar outras imagens com ela.

Crie as seguintes variáveis de texto para armazenar os URLs de imagem que utilizará neste cenário.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Esta não é a lista adequada em si, mas uma lista informal de imagens que serão adicionadas na `add images` secção do código.


### <a name="create-an-image-list"></a>Criar uma lista de imagens

Adicione o seguinte código para criar uma lista de imagens e guardar uma referência ao seu ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Adicionar imagens a uma lista

O seguinte código adiciona todas as suas imagens à lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Defina a função **de ajudante add_images** em outro lugar no seu script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Obtenha imagens na lista

O seguinte código imprime os nomes de todas as imagens da sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Atualizar detalhes da lista

Pode utilizar o ID da lista para atualizar o nome e a descrição da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Obter detalhes da lista

Utilize o seguinte código para imprimir os detalhes atuais da sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Atualizar o índice de lista

Depois de adicionar ou remover imagens, tem de atualizar o índice da lista antes de o poder utilizar para rastrear outras imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Combine imagens com a lista

A principal função das listas de imagens é comparar novas imagens e ver se existem correspondências.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Remover uma imagem da lista

O seguinte código remove um item da lista. Neste caso, é uma imagem que não corresponde à categoria de lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Remova todas as imagens de uma lista

Utilize o seguinte código para limpar uma lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Excluir a lista de imagens

Utilize o seguinte código para eliminar uma determinada lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Criar uma análise

Pode utilizar a biblioteca de clientes Do Moderador de Conteúdo Python para alimentar conteúdos na [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam revê-lo. Para saber mais sobre a ferramenta Review, consulte o guia conceptual da [ferramenta Review.](../../review-tool-user-guide/human-in-the-loop.md)

O código que se segue utiliza a classe [ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations) para criar uma revisão, recuperar o seu ID e verificar os seus dados depois de receber a entrada humana através do portal web da ferramenta Review.

### <a name="get-review-credentials"></a>Obtenha credenciais de revisão

Primeiro, inscreva-se na ferramenta 'Revisão' e recupere o nome da sua equipa. Em seguida, atribua-o à variável apropriada no código. Opcionalmente, pode configurar um ponto final de chamada para receber atualizações sobre a atividade da revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Criar uma revisão de imagem

Adicione o seguinte código para criar e publicar um revisão para o URL de imagem dado. O código guarda uma referência ao ID de revisão. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Obter detalhes da avaliação

Utilize o seguinte código para verificar os detalhes de uma determinada revisão. Depois de criar a revisão, pode ir à ferramenta Review e interagir com o conteúdo. Para obter informações sobre como fazê-lo, consulte o guia de como fazer as [avaliações.](../../review-tool-user-guide/review-moderated-images.md) Quando terminar, pode executar este código novamente, e irá recuperar os resultados do processo de revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Se usou um ponto final de retorno neste cenário, deverá receber um evento neste formato:

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

Execute a aplicação com o `python` comando no seu ficheiro quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Do Moderador de Conteúdo Python para fazer tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação, lendo um guia conceptual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](../../image-moderation-api.md)

* [O que é o Content Moderator do Azure?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).
