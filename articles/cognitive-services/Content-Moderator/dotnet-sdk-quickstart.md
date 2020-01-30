---
title: 'Início rápido: biblioteca de cliente do Content Moderator para .NET'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do Content Moderator para .NET com este guia de início rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: dccd2ebb6ac2c11e19e986d39eabda5f0ab6a8fc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774292"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Início rápido: biblioteca de cliente do Content Moderator para .NET

Introdução à biblioteca de cliente do Content Moderator para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Content Moderator é um serviço cognitiva que verifica o conteúdo de texto, imagem e vídeo para materiais potencialmente ofensivos, arriscados ou indesejáveis. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Seu aplicativo pode manipular o conteúdo sinalizado para obedecer às normas ou manter o ambiente pretendido para os usuários.

Use a biblioteca de cliente do Content Moderator para .NET para:

* [Texto moderado](#moderate-text)
* [Imagens moderadas](#moderate-images)
* [Criar uma revisão](#create-a-review)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [do NuGet ( do pacote)de](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [amostras biblioteca](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-content-moderator-azure-resource"></a>Criar um Content Moderator recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Content Moderator usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir seu recurso no [portal do Azure](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, denominado `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e `CONTENT_MODERATOR_ENDPOINT`, respectivamente.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor de texto preferencial ou IDE. 

Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome `content-moderator-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no seu editor ou IDE preferido. Adicione as seguintes instruções `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Na classe **programa** , crie variáveis para o local e a chave do ponto de extremidade do recurso como variáveis de ambiente.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Se você criou as variáveis de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar as variáveis.

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

No diretório do aplicativo, instale a biblioteca de cliente do Content Moderator para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos do SDK do .NET Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Essa classe é necessária para todas as funcionalidades de Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo adulto, informações pessoais ou faces humanas.|
|[Textmoderação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
|[Revisões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Essa classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código


Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Content Moderator para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Texto moderado](#moderate-text)
* [Imagens moderadas](#moderate-images)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, crie uma instância de objetos de cliente com o ponto de extremidade e a chave. Você não precisa de um cliente diferente para cada cenário, mas pode ajudar a manter seu código organizado.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Texto moderado

O código a seguir usa um cliente Content Moderator para analisar um corpo de texto e imprimir os resultados no console. Na raiz da sua classe de **programa** , defina os arquivos de entrada e saída:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Em seguida, na raiz do seu projeto e adicione um arquivo *textfile. txt* . Adicione seu próprio texto a este arquivo ou use o seguinte texto de exemplo:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Adicione a seguinte chamada de método ao seu método de `Main`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Em seguida, defina o método de moderação de texto em algum lugar na classe do **programa** :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Imagens moderadas

O código a seguir usa um cliente Content Moderator, juntamente com um objeto [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) , para analisar imagens remotas para conteúdo adulto e erótico.

> [!NOTE]
> Você também pode analisar o conteúdo de uma imagem local. Consulte a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para métodos e operações que funcionam com imagens locais.

### <a name="get-sample-images"></a>Obter imagens de exemplo

Defina os arquivos de entrada e saída:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Em seguida, crie o arquivo de entrada, *ImageFiles. txt*, na raiz do seu projeto. Nesse arquivo, você adiciona as URLs de imagens para analisar&mdash;uma URL em cada linha. Você pode usar as seguintes imagens de exemplo:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Passe os arquivos de entrada e saída para a seguinte chamada de método no método `Main`. Você definirá esse método em uma etapa posterior.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definir classe auxiliar

Adicione a seguinte definição de classe dentro da classe **Program** . Essa classe interna manipulará os resultados de moderação de imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definir o método de moderação de imagem

O método a seguir itera as URLs de imagem em um arquivo de texto, cria uma instância de **EvaluationData** e analisa a imagem para conteúdo adulto/erótico, texto e faces humanas. Em seguida, ele adiciona a instância **EvaluationData** final a uma lista e grava a lista completa de dados retornados no console.

#### <a name="iterate-through-image-urls"></a>Iterar por meio de URLs de imagem

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analisar conteúdo

Para obter mais informações sobre os atributos de imagem para os quais Content Moderator telas, consulte o guia de [conceitos de moderação de imagem](./image-moderation-api.md) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Gravar resultados de moderação em arquivo

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Criar uma revisão

Você pode usar o SDK do .NET Content Moderator para alimentar o conteúdo na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam examiná-lo. Para saber mais sobre a ferramenta de revisão, consulte o [guia conceitual da ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md).

O método nesta seção usa a classe [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) para criar uma revisão, recuperar sua ID e verificar seus detalhes depois de receber entrada humana por meio do portal da Web da ferramenta de revisão. Ele registra todas essas informações em um arquivo de texto de saída. Chame o método do seu método de `Main`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Obter imagens de exemplo

Declare a seguinte matriz na raiz da sua classe de **programa** . Essa variável faz referência a uma imagem de exemplo a ser usada para criar a revisão.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Obter credenciais de revisão

Entre na ferramenta de [revisão](https://contentmoderator.cognitive.microsoft.com) e recupere o nome da equipe. Em seguida, atribua-o à variável apropriada na classe **programa** . Opcionalmente, você pode configurar um ponto de extremidade de retorno de chamada para receber atualizações sobre a atividade da revisão.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definir classe auxiliar

Adicione a seguinte definição de classe dentro de sua classe de **programa** . Essa classe será usada para representar uma única instância de revisão que é enviada para a ferramenta de revisão.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definir método auxiliar

Adicione o seguinte método à classe **Programa**. Esse método gravará os resultados das consultas de revisão no arquivo de texto de saída.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definir o método de criação de revisão

Agora você está pronto para definir o método que manipulará a criação e a consulta da revisão. Adicione um novo método, **Createrevis**e defina as seguintes variáveis locais.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Postar revisões para a ferramenta de revisão

Em seguida, adicione o código a seguir para iterar pelas imagens de exemplo fornecidas, adicionar metadados e enviá-los para a ferramenta de revisão em um único lote. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

O objeto retornado da chamada à API conterá valores de ID exclusivos para cada imagem carregada. O código a seguir analisa essas IDs e as usa para consultar Content Moderator para o status de cada imagem no lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Obter detalhes da revisão

O código a seguir faz com que o programa aguarde a entrada do usuário. Ao chegar a essa etapa no tempo de execução, você pode ir para a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) por conta própria, verificar se a imagem de exemplo foi carregada e interagir com ela. Para obter informações sobre como interagir com uma revisão, consulte o [Guia de instruções](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)de análise. Quando tiver terminado, você poderá pressionar qualquer tecla para continuar o programa e recuperar os resultados do processo de revisão.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

Execute o aplicativo no diretório do aplicativo com o comando `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar o Content Moderator biblioteca .NET para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Azure Content Moderator?](./overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs).
