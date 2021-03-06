---
title: 'Quickstart: Biblioteca de clientes QnA Maker para .NET'
description: Este quickstart mostra como começar com a biblioteca cliente do QnA Maker para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: aac57f4ca173a7ac94c64884fa1d2db4a478c3f3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609481"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Utilize a biblioteca cliente QnA Maker para .NET para:

 * Criar uma base de conhecimentos
 * Atualizar uma base de conhecimentos
 * Publicar uma base de conhecimentos
 * Obtenha a chave final do tempo de execução da previsão
 * Aguarde por uma tarefa de longa duração
 * Descarregue uma base de conhecimentos
 * Obtenha uma resposta de uma base de conhecimento
 * Eliminar uma base de conhecimentos

[Documentação de referência](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1)  |  [Amostras de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Utilize a biblioteca cliente QnA Maker para .NET para:

 * Criar uma base de conhecimentos
 * Atualizar uma base de conhecimentos
 * Publicar uma base de conhecimentos
 * Aguarde por uma tarefa de longa duração
 * Descarregue uma base de conhecimentos
 * Obtenha uma resposta de uma base de conhecimento
 * Eliminar uma base de conhecimentos

[Documentação de referência](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1)  |  [Amostras de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e nome de recurso. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e nome de recurso do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Você vai colar a sua chave e nome de recurso no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final.
    * NOTA: Certifique-se de selecionar a caixa de verificação **Gerida.**
    * Depois de o seu recurso QnA Maker ser implementado, selecione **Ir para o recurso**. Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configuração

### <a name="cli"></a>CLI

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `qna-maker-quickstart` . Este comando cria um projeto "Hello World" C# simples com um único ficheiro de origem: *programa.cs*.

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

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs)que contém os exemplos de código neste arranque rápido.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs)que contém os exemplos de código neste arranque rápido.

---

### <a name="using-directives"></a>Utilização de diretivas

A partir do diretório do projeto, abra o arquivo *.cs programa* e adicione as `using` seguintes diretivas:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Chave de subscrição e pontos finais de recursos

No método da `Main` aplicação, adicione variáveis e código, mostrados na secção seguinte, para utilizar as tarefas comuns neste arranque rápido.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

- Utilizamos a chave de subscrição e a chave de autoria interchangemente. Para obter mais detalhes sobre a chave de autoria, siga [as Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. Vá ao portal Azure e encontre o recurso QnA Maker que criou nos pré-requisitos. Clique na página **Keys e Endpoint,** sob **gestão de recursos** para localizar a tecla de autoria (subscrição) e o ponto final do Criador QnA.

 ![QnA Maker Authoring Endpoint](../media/keys-endpoint.png)

- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.azurewebsites.net` formato. Vá ao portal Azure e encontre o recurso QnA Maker que criou nos pré-requisitos. Clique na página **modelo de exportação,** em **automatização** para localizar o ponto de fim de tempo de execução.

 ![Ponto final de runtime do fabricante QnA](../media/runtime-endpoint.png)
      
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

- Utilizamos a chave de subscrição e a chave de autoria interchangemente. Para obter mais detalhes sobre a chave de autoria, siga [as Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. Vá ao portal Azure e encontre o recurso QnA Maker que criou nos pré-requisitos. Clique na página **Keys e Endpoint,** sob **gestão de recursos** para localizar a tecla de autoria (subscrição) e o ponto final do Criador QnA.

 ![QnA Maker Authoring Endpoint](../media/keys-endpoint.png)

- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Modelos de objetos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[O Fabricante QnA](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../how-to/use-active-learning.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[O Fabricante QnA](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) utiliza o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar, transferir e consultar a base de conhecimentos.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da autoria do QnA Maker é um objeto [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) que autentica a Azure usando microsoft.rest.ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize a propriedade [Base de Conhecimento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) para criar, gerir e publicar a sua base de conhecimentos.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método normalmente devolve um objeto JSON indicando o estado. Para operações de longa duração, a resposta é a identificação da operação. Ligue para o [cliente. Operações.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) método com o ID de operação para determinar o [estado do pedido](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O cliente QnA Maker de previsão é um objeto [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) que autentica para Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave de tempo de previsão, devolvida da chamada do cliente autoria, `client.EndpointKeys.GetKeys` após a publicação da base de conhecimento.

Utilize o método [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) para obter uma resposta a partir do tempo de funcionação da consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Um recurso gerido pelo QnA Maker não requer a utilização do objeto **QnAMakerRuntimeClient.** Em vez disso, você chama a [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase). [Gerar Método Deseurose.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync)

---

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca cliente QnA Maker para .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

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

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

* [Autenticar o cliente de autoria](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Criar uma base de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Descarregue uma base de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter o estado de uma operação](#get-status-of-an-operation)
* [Gerar uma resposta a partir da base de conhecimento](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimentos

Instantiar um objeto de cliente com a sua chave, e usá-lo com o seu recurso para construir o ponto final para criar um [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) com o seu ponto final e chave. Crie um objeto [ServiceClientCredentials.](/dotnet/api/microsoft.rest.serviceclientcredentials)

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

O passo de criação também inclui propriedades para a base de conhecimentos:
* `defaultAnswerUsedForExtraction` - o que é devolvido quando não se encontra uma resposta
* `enableHierarchicalExtraction` - criar automaticamente relações rápidas entre pares QnA extraídos
* `language` - ao criar a primeira base de conhecimentos de um recurso, desaveja o idioma a utilizar no índice de Pesquisa de Azure.

Ligue para o método [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) e, em seguida, passe o ID de operação devolvido para o método [MonitorOperation](#get-status-of-an-operation) para sondar o estado.

A linha final do seguinte código devolve o ID da base de conhecimento da resposta da MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Certifique-se de que a [`MonitorOperation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos transmitindo o ID da base de conhecimento e um [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) contendo [adicionar,](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd) [atualizar](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)e [eliminar](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) objetos DTO para o método [UpdateAsync.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) Utilize o método [MonitorOperation](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Certifique-se de que inclui a [`MonitorOperation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a atualizar com sucesso uma base de conhecimento.

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) para descarregar a base de dados como uma lista de [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos utilizando o método [PublishAsync.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) Isto pega no modelo atual guardado e treinado, referenciado pelo ID da base de conhecimento, e publica-o no seu ponto final. Este é um passo necessário para consultar a sua base de conhecimentos.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Obtenha chave de tempo de execução de consulta

Uma vez publicada uma base de conhecimento, precisa da chave de tempo de consulta para consultar o tempo de execução. Esta não é a mesma chave usada para criar o objeto cliente original.

Utilize o método [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para obter a classe [EndpointKeysDTO.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto)

Utilize qualquer uma das propriedades chave devolvidas no objeto para consultar a base de conhecimentos.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

Uma chave de tempo de execução é necessária para consultar a sua base de conhecimentos.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de uma aprendizagem ativa.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Utilize o QnAMakerRuntimeClient para:
* obter uma resposta a partir da base de conhecimento
* para enviar novas perguntas sugeridas para a base de conhecimento para [a aprendizagem ativa.](../concepts/active-learning-suggestions.md)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimentos publicada utilizando o [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [Gerar Método Deseurose.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) Este método aceita o ID da base de conhecimento e o [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Aceda a propriedades adicionais do QueryDTO, tal [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) para usar no seu chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimentos publicada utilizando a [base de conhecimento qnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase). [Gerar Método Deseurose.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) Este método aceita o ID da base de conhecimento e o [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Aceda a propriedades adicionais do QueryDTO, tal [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) e [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) para usar no seu chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Este é um exemplo simples consultando a base de conhecimentos. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimentos utilizando o método [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) com um parâmetro do ID da base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) seja devolvida. Utilize o ID de [funcionamento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

O _loop_ e _Task.Delay_ no bloco de código que se segue são utilizados para simular a lógica de retrip. Estes devem ser substituídos pela sua própria lógica de relemgar.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `dotnet run` comando do seu diretório de candidaturas.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---
