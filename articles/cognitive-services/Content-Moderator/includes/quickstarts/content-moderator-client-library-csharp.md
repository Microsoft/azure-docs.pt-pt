---
title: Moderador de Conteúdo .NET biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
description: Comece com a biblioteca de clientes Moderador de Conteúdo para .NET com este quickstart.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 50572c037cc7927a06bcf8d95305353238989ae9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187351"
---
Inicie-se com a biblioteca de clientes Moderador de Conteúdo para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Content Moderator é um serviço cognitivo que verifica o conteúdo de texto, imagem e vídeo para material potencialmente ofensivo, arriscado ou de outra forma indesejável. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. A sua aplicação pode então lidar com conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente pretendido para os utilizadores.

Utilize a biblioteca de clientes Moderador de Conteúdo para .NET para:

* [Texto moderado](#moderate-text)
* [Imagens moderadas](#moderate-images)
* [Criar uma revisão](#create-a-review)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | da biblioteca[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Samples](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configuração

### <a name="create-a-content-moderator-azure-resource"></a>Criar um recurso Azure moderador de conteúdo

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para moderador de conteúdo utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e, `CONTENT_MODERATOR_ENDPOINT`respectivamente.

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor de texto preferido ou IDE. 

Numa janela de consola (como cmd, PowerShell `dotnet new` ou Bash), utilize o comando `content-moderator-quickstart`para criar uma nova aplicação de consola com o nome . Este comando cria um simples projeto C# "Hello World" com um único ficheiro fonte: *Program.cs*.

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

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor ou IDE preferido. Adicione as seguintes instruções `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Na aula do **Programa,** crie variáveis para a localização final do seu recurso e chave como variáveis ambientais.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Se criou as variáveis ambientais depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder às variáveis.

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Moderador de Conteúdo para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Content Moderator .NET.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Esta aula é necessária para todas as funcionalidades moderadoras de conteúdo. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes.|
|[Imagem Moderação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Esta aula fornece a funcionalidade para analisar imagens para conteúdos para adultos, informações pessoais ou rostos humanos.|
|[TextoModeração](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Esta aula fornece a funcionalidade para analisar texto para linguagem, profanação, erros e informações pessoais.|
|[Revisões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código


Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Moderador de Conteúdo para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Texto moderado](#moderate-text)
* [Imagens moderadas](#moderate-images)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, objetos instantâneos do cliente com o seu ponto final e chave. Não precisa de um cliente diferente para cada cenário, mas pode ajudar a manter o seu código organizado.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Texto moderado

O código seguinte utiliza um cliente moderador de conteúdo para analisar um corpo de texto e imprimir os resultados para a consola. Na raiz da sua classe **Program,** defina ficheiros de entrada e saída:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Em seguida, na raiz do seu projeto e adicione um ficheiro *TextFile.txt.* Adicione o seu próprio texto a este ficheiro ou utilize o seguinte texto da amostra:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Adicione a seguinte chamada `Main` de método ao seu método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Em seguida, defina o método de moderação de texto algures na sua aula de **Programa:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Imagens moderadas

O código seguinte utiliza um cliente Moderador de Conteúdo, juntamente com um objeto [ImageModeration,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) para analisar imagens remotas para conteúdo adulto e picante.

> [!NOTE]
> Também pode analisar o conteúdo de uma imagem local. Consulte a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para métodos e operações que funcionem com imagens locais.

### <a name="get-sample-images"></a>Obtenha imagens de amostra

Defina os seus ficheiros de entrada e saída:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Em seguida, crie o ficheiro de entrada, *ImageFiles.txt,* na raiz do seu projeto. Neste ficheiro, adiciona os URLs de&mdash;imagens para analisar um URL em cada linha. Pode utilizar as seguintes imagens da amostra:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Passe os seus ficheiros de entrada e `Main` saída para a chamada do método seguinte. Definirá este método mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definir classe de ajudante

Adicione a seguinte definição de classe dentro da classe **Programa.** Esta classe interior lidará com os resultados da moderação da imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Defina o método de moderação da imagem

O método seguinte iterates através dos URLs de imagem em um ficheiro de texto, cria uma instância **De AvaliaçãoData,** e analisa a imagem para conteúdo adulto/picante, texto e rostos humanos. Em seguida, adiciona a instância final **de EvaluationData** a uma lista e escreve a lista completa de dados devolvidos à consola.

#### <a name="iterate-through-image-urls"></a>Iterado através de URLs de imagem

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analisar conteúdo

Para obter mais informações sobre os atributos de imagem para os quais o Moderador de Conteúdo tela, consulte o guia de conceitos de [moderação da imagem.](../../image-moderation-api.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Escreva resultados de moderação para arquivar

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Criar uma revisão

Pode utilizar a biblioteca de clientes Moderador de Conteúdo .NET para alimentar conteúdos na [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam revê-lo. Para saber mais sobre a ferramenta Review, consulte o guia conceptual da [ferramenta Rever](../../review-tool-user-guide/human-in-the-loop.md).

O método nesta secção utiliza a classe [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) para criar uma revisão, recuperar o seu ID e verificar os seus detalhes depois de receber a entrada humana através do portal web da ferramenta Review. Regista toda esta informação num ficheiro de texto de saída. Ligue para o `Main` método do seu método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Obtenha imagens de amostra

Declare a seguinte matriz na raiz da sua aula de **Programa.** Esta variável refere uma imagem de amostra para criar a revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Obtenha credenciais de revisão

Inscreva-se na [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) e recupere o nome da sua equipa. Em seguida, atribua-o à variável apropriada na classe **Programa.** Opcionalmente, pode configurar um ponto final de callback para receber atualizações sobre a atividade da revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definir classe de ajudante

Adicione a seguinte definição de classe dentro da sua aula de **Programa.** Esta classe será utilizada para representar uma única instância de revisão que é submetida à ferramenta De revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definir método de ajudante

Adicione o seguinte método à classe **Programa**. Este método irá escrever os resultados das consultas de revisão ao ficheiro de texto de saída.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definir o método de criação de revisão

Agora está pronto para definir o método que irá lidar com a criação de revisão e consulta. Adicione um novo método, **CreateReviews,** e defina as seguintes variáveis locais.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Post reviews to the Review tool

Em seguida, adicione o seguinte código ao iterado através das imagens da amostra, adicione metadados e envie-os para a ferramenta Review num único lote. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

O objeto devolvido da chamada API conterá valores de IDENTIFICAção únicos para cada imagem enviada. O código seguinte analisa estes IDs e, em seguida, usa-os para consultar o Moderador de Conteúdo para o estado de cada imagem no lote.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Obtenha detalhes da revisão

O código seguinte faz com que o programa aguarde a entrada do utilizador. Quando chegar a este passo no tempo de execução, pode ir à [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) por si mesmo, verificar se a imagem da amostra foi carregada e interagir com ela. Para obter informações sobre como interagir com uma análise, consulte o guia de [comentários](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Quando terminar, pode premir qualquer tecla para continuar o programa e recuperar os resultados do processo de revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

Execute a aplicação a `dotnet run` partir do seu diretório de candidatura com o comando.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Content Moderator .NET para fazer tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação através da leitura de um guia conceptual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Content Moderator do Azure?](../../overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
