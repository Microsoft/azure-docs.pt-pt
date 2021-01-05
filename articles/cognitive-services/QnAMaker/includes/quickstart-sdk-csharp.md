---
title: 'Quickstart: Biblioteca de clientes QnA Maker para .NET'
description: Este quickstart mostra como começar com a biblioteca cliente do QnA Maker para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 05349d8339b9f2b8472e1b5384f213c92e158e5d
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812747"
---
Utilize a biblioteca cliente QnA Maker para .NET para:

 * Criar uma base de conhecimentos
 * Atualizar uma base de conhecimentos
 * Publicar uma base de conhecimentos
 * Obtenha a chave final do tempo de execução da previsão
 * Aguarde por uma tarefa de longa duração
 * Descarregue uma base de conhecimentos
 * Obter resposta
 * Eliminar uma base de conhecimentos

[Documentação de referência](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [Amostras de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e nome de recurso. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e nome de recurso do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Você vai colar a sua chave e nome de recurso no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Utilizando o Visual Studio, crie uma aplicação .NET Core e instale a biblioteca do cliente clicando corretamente na solução no **Solution Explorer** e selecionando **Pacotes De Gestão de NuGet**. No gestor de pacotes que abre **selecione Navegar,** verificar **Incluir pré-relançar,** e procurar `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Selecione a versão `2.0.0-preview.1` e, em seguida, **instale**.

#### <a name="cli"></a>[CLI](#tab/cli)

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `qna-maker-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
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

Dentro do diretório de aplicações, instale a biblioteca cliente do QnA Maker para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs)que contém os exemplos de código neste arranque rápido.

A partir do diretório do projeto, abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

No método da `Main` aplicação, adicione variáveis e código, mostrados na secção seguinte, para utilizar as tarefas comuns neste arranque rápido.

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**
> Precisa de toda a chave para criar a sua base de conhecimentos. Só precisa do nome de recurso do ponto final. O formato é `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>Modelos de objetos

O Fabricante QnA usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../concepts/active-learning-suggestions.md)

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da autoria do QnA Maker é um objeto [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) que autentica a Azure usando microsoft.rest.ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize a propriedade [Base de Conhecimento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) para criar, gerir e publicar a sua base de conhecimentos.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método normalmente devolve um objeto JSON indicando o estado. Para operações de longa duração, a resposta é a identificação da operação. Ligue para o [cliente. Operações.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) método com o ID de operação para determinar o [estado do pedido](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

O cliente QnA Maker de previsão é um objeto [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) que autentica para Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave de tempo de previsão, devolvida da chamada do cliente autoria, `client.EndpointKeys.GetKeys` após a publicação da base de conhecimento.

Utilize o método [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) para obter uma resposta a partir do tempo de funcionação da consulta.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca cliente QnA Maker para .NET:

* [Autenticar o cliente de autoria](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Criar uma base de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Descarregue uma base de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obtenha chave de tempo de execução de consulta](#get-query-runtime-key)
* [Obter o estado de uma operação](#get-status-of-an-operation)
* [Autenticar o cliente de consulta](#authenticate-the-runtime-for-generating-an-answer)
* [Gerar uma resposta a partir da base de conhecimento](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimentos

Instantiar um objeto de cliente com a sua chave, e usá-lo com o seu recurso para construir o ponto final para criar um [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) com o seu ponto final e chave. Crie um objeto [ServiceClientCredentials.](/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

O passo de criação também inclui propriedades para a base de conhecimentos:
* `defaultAnswerUsedForExtraction` - o que é devolvido quando não se encontra uma resposta
* `enableHierarchicalExtraction` - criar automaticamente relações rápidas entre pares QnA extraídos
* `language` - ao criar a primeira base de conhecimentos de um recurso, desaveja o idioma a utilizar no índice de Pesquisa de Azure.

Ligue para o método [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) e, em seguida, passe o ID de operação devolvido para o método [MonitorOperation](#get-status-of-an-operation) para sondar o estado.

A linha final do seguinte código devolve o ID da base de conhecimento da resposta da MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

Certifique-se de que a [`MonitorOperation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos transmitindo o ID da base de conhecimento e um [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) contendo [adicionar,](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet) [atualizar](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)e [eliminar](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) objetos DTO para o método [UpdateAsync.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) Utilize o método [MonitorOperation](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Certifique-se de que inclui a [`MonitorOperation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a atualizar com sucesso uma base de conhecimento.

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) para descarregar a base de dados como uma lista de [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos utilizando o método [PublishAsync.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) Isto pega no modelo atual guardado e treinado, referenciado pelo ID da base de conhecimento, e publica-o no seu ponto final. Este é um passo necessário para consultar a sua base de conhecimentos.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>Obtenha chave de tempo de execução de consulta

Uma vez publicada uma base de conhecimento, precisa da chave de tempo de consulta para consultar o tempo de execução. Esta não é a mesma chave usada para criar o objeto cliente original.

Utilize o método [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para obter a classe [EndpointKeysDTO.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet)

Utilize qualquer uma das propriedades chave devolvidas no objeto para consultar a base de conhecimentos.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

Uma chave de tempo de execução é necessária para consultar a sua base de conhecimentos.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de uma aprendizagem ativa.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Utilize o QnAMakerRuntimeClient para:
* obter uma resposta a partir da base de conhecimento
* para enviar novas perguntas sugeridas para a base de conhecimento para [a aprendizagem ativa.](../concepts/active-learning-suggestions.md)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

### <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Gere uma resposta a partir de uma base de conhecimentos publicada utilizando o [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [Gerar Método Deseurose.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) Este método aceita o ID da base de conhecimento e o [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Aceda a propriedades adicionais do QueryDTO, tal [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) para usar no seu chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]


### <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Gere uma resposta a partir de uma base de conhecimentos publicada utilizando a [base de conhecimento qnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet-preview). [Gerar Método Deseurose.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync?view=azure-dotnet-preview) Este método aceita o ID da base de conhecimento e o [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet-preview). Aceda a propriedades adicionais do QueryDTO, tal [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) e [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) para usar no seu chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Este é um exemplo simples consultando a base de conhecimentos. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)


## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimentos utilizando o método [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) com um parâmetro do ID da base de conhecimento.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) seja devolvida. Utilize o ID de [funcionamento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

O _loop_ e _Task.Delay_ no bloco de código que se segue são utilizados para simular a lógica de retrip. Estes devem ser substituídos pela sua própria lógica de relemgar.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `dotnet run` comando do seu diretório de candidaturas.

```dotnetcli
dotnet run
```

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).
