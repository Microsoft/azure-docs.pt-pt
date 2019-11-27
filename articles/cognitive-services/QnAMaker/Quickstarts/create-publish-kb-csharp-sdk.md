---
title: 'Início rápido: biblioteca de cliente do QnA Maker para .NET'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do QnA Maker para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 1c403d4d9b5c95c1cb1079b951a26dcaabb805fe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405876"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Início rápido: biblioteca de cliente do QnA Maker para .NET

Introdução à biblioteca de cliente do QnA Maker para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos. 

Use a biblioteca de cliente do QnA Maker para .NET para:

* Criar uma base de dados de conhecimento 
* Gerenciar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Gerar uma resposta da base de dados de conhecimento

[Documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [ C# amostras](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp) do NuGet ( [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [do pacote) | de](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) biblioteca

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-qna-maker-azure-resource"></a>Criar um QnA Maker recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para QnA Maker usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. 

Depois de obter uma chave e um ponto de extremidade para o recurso, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, denominada `QNAMAKER_SUBSCRIPTION_KEY`. O nome do recurso é usado como parte da URL do ponto de extremidade.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome `qna-maker-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*. 

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```dotnetcli
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

### <a name="install-the-sdk"></a>Instalar o SDK

No diretório do aplicativo, instale a biblioteca de cliente do QnA Maker para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.


## <a name="object-model"></a>Modelo de objeto

O QnA Maker Client é um objeto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Depois que o cliente for criado, use a propriedade [da base de dados de conhecimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) para criar, gerenciar e publicar sua base de dados de conhecimento. 

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chamar o [cliente. O método Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) com a ID da operação para determinar o [status da solicitação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente QnA Maker para .NET:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Baixar uma base de dados de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter o status de uma operação](#get-status-of-an-operation)
* [Gerar uma resposta da base de dados de conhecimento](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no seu editor ou IDE preferido. Substitua o código de `using` existente pelas seguintes diretivas de `using`:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

No método **Main** , crie uma variável para a chave do Azure do recurso extraída de uma variável de ambiente chamada `QNAMAKER_SUBSCRIPTION_KEY`. Se você criou a variável de ambiente depois que o aplicativo é iniciado, o editor, IDE ou Shell em execução precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente.

Em seguida, crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) com sua chave e use-o com seu ponto de extremidade para criar um objeto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) .

Altere a variável de **ponto de extremidade** , `<your-custom-domain>`, para o nome do seu domínio personalizado. Esse local é encontrado na página **visão geral** do recurso de QnA Maker no portal do Azure.

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

No método **Main** , crie uma variável para a autenticação do seu recurso extraído de variáveis de ambiente chamadas `QNAMAKER_ENDPOINT_HOSTNAME` e `QNAMAKER_ENDPOINT_KEY`. Quando você publica sua base de dados de conhecimento, esses valores são retornados. Depois de publicar, você pode encontrar essas configurações na página **configurações** do portal de QnA Maker. 

Crie um [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) para consultar a base de dados de conhecimento para gerar uma resposta ou um treinamento do aprendizado ativo.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) .
* Para **arquivos**, use o objeto [encampoto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) . 
* Para **URLs**, use uma lista de cadeias de caracteres.

Chame o método [createasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) e, em seguida, passe a ID da operação retornada para o método [MonitorOperation](#get-status-of-an-operation) para sondar o status. 

A linha final do código a seguir retorna a ID da base de dados de conhecimento da resposta de MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Certifique-se de incluir a função [`MonitorOperation`](#get-status-of-an-operation) , referenciada no código acima, para criar com êxito uma base de dados de conhecimento. 

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Você pode atualizar uma base de dados de conhecimento passando a ID da base de dados de conhecimento e um [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) contendo [Adicionar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [Atualizar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)e [excluir](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) objetos dto para o método [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) . Use o método [MonitorOperation](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Certifique-se de incluir a função [`MonitorOperation`](#get-status-of-an-operation) , referenciada no código acima, para atualizar com êxito uma base de dados de conhecimento. 

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) para baixar o banco de dados como uma lista de [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Isso _não_ é equivalente à exportação do QnA Maker portal na página de **configurações** porque o resultado desse método não é um arquivo TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) . Isso usa o modelo atual salvo e treinado, referenciado pela ID da base de dados de conhecimento e o publica em um ponto de extremidade. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gerar uma resposta de uma base de dados de conhecimento publicada usando o [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). Método [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) . Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Acesse as propriedades adicionais do QueryDTO, como [parte superior](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [contexto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) para usar em seu bot de chat. 

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) com um parâmetro da ID da base de dados de conhecimento. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente que, em vez de aguardar a conclusão do processo, uma [operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) é retornada. Use a [ID da operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) da operação para sondagem (com lógica de repetição) para determinar o status do método original. 

O _loop_ e a _tarefa. o atraso_ no bloco de código a seguir são usados para simular a lógica de repetição. Eles devem ser substituídos por sua própria lógica de repetição. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o comando `dotnet run` no diretório do aplicativo.

Todos os trechos de código neste artigo estão [disponíveis](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) e podem ser executados como um único arquivo.

```dotnetcli
dotnet run
```

O [código-fonte deste guia de início rápido](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) está disponível C# no repositório GitHub de exemplos de QnA Maker.

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Tutorial: criar e responder a um KB](../tutorials/create-publish-query-in-portal.md)

* [O que é o API de QnA Maker?](../Overview/overview.md)
* [Editar uma base de dados de conhecimento](../how-to/edit-knowledge-base.md)
* [Obter análise de uso](../how-to/get-analytics-knowledge-base.md)
