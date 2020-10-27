---
title: Início rápido da biblioteca de clientes do Moderador de Conteúdo .NET
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprenda a começar com a biblioteca de clientes Azure Content Moderador para .NET. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 2db80cdba778d868d90d5278005791257acb0ed3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548217"
---
Começa com a biblioteca de clientes Azure Content Moderador para .NET. Siga estes passos para instalar o pacote NuGet e experimente o código de exemplo para tarefas básicas. 

Content Moderator é um serviço de IA que permite lidar com conteúdos potencialmente ofensivos, arriscados ou indesejáveis. Utilize o serviço de moderação de conteúdo movido a IA para digitalizar texto, imagem e vídeos e aplicar automaticamente as bandeiras de conteúdo. Em seguida, integre a sua app com a ferramenta Review, um ambiente moderador on-line para uma equipa de revisores humanos. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.

Utilize a biblioteca de clientes Content Moderador para .NET para:

* [Texto moderado](#moderate-text)
* [Imagens moderadas](#moderate-images)
* [Criar uma análise](#create-a-review)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Amostras](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" crie um recurso De Moderador de Conteúdo crie um recurso De Moderador de Conteúdo no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao Moderador de Conteúdo. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Utilizando o Visual Studio, crie uma nova aplicação .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Depois de criar um novo projeto, instale a biblioteca do cliente clicando corretamente na solução de projeto no **Solution Explorer** e selecione **Gerir pacotes NuGet** . No gestor de pacotes que abre **selecione Navegar,** verificar **Incluir pré-relançar,** e procurar `Microsoft.Azure.CognitiveServices.ContentModerator` . Selecione a versão `2.0.0` e, em seguida, **instale** . 

#### <a name="cli"></a>[CLI](#tab/cli)

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `content-moderator-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *Program.cs* .

```console
dotnet new console -n content-moderator-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Dentro do diretório de aplicações, instale a biblioteca de clientes Content Moderador para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)que contém os exemplos de código neste arranque rápido.

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor preferido ou IDE. Adicione as seguintes instruções `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Na classe **Programa,** crie variáveis para a chave e ponto final do seu recurso.

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso de Moderador de Conteúdo que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes** . Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


No método da `main()` aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vais criar isto mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Content Moderador .NET.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Esta classe é necessária para todas as funcionalidades do Moderador de Conteúdo. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes.|
|[Imagem Dedesederação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Esta classe fornece a funcionalidade de análise de imagens para conteúdo adulto, informações pessoais ou rostos humanos.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Esta classe fornece a funcionalidade de análise de texto para linguagem, profanação, erros e informações pessoais.|
|[Revisões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e avaliações humanas.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes do Moderador de Conteúdo para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Texto moderado](#moderate-text)
* [Imagens moderadas](#moderate-images)
* [Criar uma análise](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instantâneas objetos do cliente com o seu ponto final e chave.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Texto moderado

O código que se segue utiliza um cliente Moderador de Conteúdo para analisar um conjunto de textos e imprimir os resultados na consola. Na raiz da sua classe **Program,** defina os ficheiros de entrada e saída:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Em seguida, na raiz do seu projeto adicione um arquivo *TextFile.txt.* Adicione o seu próprio texto a este ficheiro ou use o seguinte texto de amostra:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Em seguida, defina o método de moderação de texto em algum lugar na sua classe **programa:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Imagens moderadas

O código que se segue utiliza um cliente Moderador de Conteúdo, juntamente com um objeto [ImageModeration,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) para analisar imagens remotas para conteúdo adulto e picante.

> [!NOTE]
> Também pode analisar o conteúdo de uma imagem local. Consulte a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para métodos e operações que funcionam com imagens locais.

### <a name="get-sample-images"></a>Obtenha imagens de amostra

Defina os seus ficheiros de entrada e saída na raiz da sua classe **Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Em seguida, crie o ficheiro de entrada, *ImageFiles.txt,* na raiz do seu projeto. Neste ficheiro, adiciona-se os URLs de imagens para analisar &mdash; um URL em cada linha. Pode utilizar as seguintes imagens de amostra:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definir classe de ajudante

Adicione a seguinte definição de classe dentro da classe **Programa.** Esta classe interior lidará com os resultados da moderação da imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definir o método de moderação de imagem

O método a seguir itera através dos URLs de imagem num ficheiro de texto, cria uma instância **DeR de Avaliação** e analisa a imagem para conteúdo adulto/picante, texto e rostos humanos. Em seguida, adiciona a instância Final DeData de **Avaliação** a uma lista e escreve a lista completa de dados devolvidos à consola.

#### <a name="iterate-through-images"></a>Iterar através de imagens

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analisar conteúdo

Para obter mais informações sobre os atributos de imagem para os quais o Moderador de Conteúdo é exibido, consulte o guia [de conceitos de moderação de imagem.](../../image-moderation-api.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Escreva resultados de moderação para arquivar

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Criar uma análise

Pode utilizar a biblioteca de clientes Content Moderador .NET para alimentar conteúdos na [ferramenta 'Revisão',](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam revê-lo. Para saber mais sobre a ferramenta Review, consulte o guia conceptual da [ferramenta Review.](../../review-tool-user-guide/human-in-the-loop.md)

O método nesta secção utiliza a classe [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) para criar uma revisão, recuperar o seu ID e verificar os seus detalhes depois de receber a entrada humana através do portal web da ferramenta Review. Regista toda esta informação num ficheiro de texto de saída. 

### <a name="get-sample-images"></a>Obtenha imagens de amostra

Declare a seguinte matriz na raiz da sua aula **de Programa.** Esta variável refere uma imagem de amostra para usar para criar a revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Obtenha credenciais de revisão

Inscreva-se na [ferramenta 'Revisão'](https://contentmoderator.cognitive.microsoft.com) e recupere o nome da sua equipa. Em seguida, atribua-o à variável apropriada na classe **Programa.** Opcionalmente, pode configurar um ponto final de chamada para receber atualizações sobre a atividade da revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definir classe de ajudante

Adicione a seguinte definição de classe dentro da sua classe **Program.** Esta classe será usada para representar uma única instância de revisão que é submetida à ferramenta Review.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definir método de ajuda

Adicione o seguinte método à classe **Programa** . Este método escreverá os resultados das consultas de revisão no ficheiro de texto de saída.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definir o método de criação de revisão

Agora está pronto para definir o método que irá lidar com a criação e consulta de revisão. Adicione um novo método, **CreateReviews,** e defina as seguintes variáveis locais.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publicar comentários na ferramenta Review

Em seguida, adicione o seguinte código para iterar através das imagens de amostras dadas, adicionar metadados e enviá-los para a ferramenta 'Revisão' num único lote. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

O objeto devolvido da chamada API conterá valores de ID únicos para cada imagem carregada. O código seguinte analisa estes IDs e, em seguida, usa-os para consultar o Moderador de Conteúdo para o estado de cada imagem no lote.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Obter detalhes da avaliação

O seguinte código faz com que o programa aguarde a entrada do utilizador. Quando chegar a este passo no tempo de execução, pode ir à [ferramenta Review,](https://contentmoderator.cognitive.microsoft.com) verificar se a imagem da amostra foi carregada e interagir com ela. Para obter informações sobre como interagir com uma revisão, consulte o [guia de como fazer comentários.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images) Quando terminar, pode premir qualquer tecla para continuar o programa e recuperar os resultados do processo de revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Executar a aplicação clicando no botão **Debug** na parte superior da janela IDE.

#### <a name="cli"></a>[CLI](#tab/cli)

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Content Moderator .NET para fazer tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação, lendo um guia conceptual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Content Moderator do Azure?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
