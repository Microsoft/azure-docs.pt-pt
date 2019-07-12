---
title: 'Início rápido: Biblioteca de clientes do QnA Maker para .NET'
titlesuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do QnA Maker para .NET. Siga estes passos para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: f38cbfa0efd3b9be9ca091942dca7ffcd91b6019
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828121"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Início rápido: Biblioteca de clientes do QnA Maker para .NET

Introdução à biblioteca de cliente do QnA Maker para .NET. Siga estes passos para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos. 

Utilize a biblioteca de cliente do QnA Maker para .NET para:

* Criar uma base de dados de conhecimento 
* Gerir uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento

[Documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ C# exemplos](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [criá-lo gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Como configurar

### <a name="create-a-qna-maker-azure-resource"></a>Criar um recurso do Azure do QnA Maker

Serviços cognitivos do Azure são representados por recursos do Azure que subscrever. Criar um recurso para utilizar o QnA Maker a [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no seu computador local. 

Após obter uma chave do seu recurso [criar uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, com o nome `QNAMAKER_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicação

Crie uma nova aplicação de .NET Core no seu editor preferencial ou IDE. 

Numa janela de consola (por exemplo, cmd, o PowerShell ou Bash), utilize o dotnet `new` comando para criar uma nova aplicação de consola com o nome `qna-maker-quickstart`. Este comando cria um simples "Hello World" C# projeto com um único arquivo de origem: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
```

Altere o diretório para a pasta da aplicação criada recentemente. Pode criar a aplicação com:

```console
dotnet build
```

O resultado da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```


### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório de aplicativo, instale a biblioteca de cliente do QnA Maker para o .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Se estiver a utilizar o IDE do Visual Studio, a biblioteca de clientes está disponível como um pacote de NuGet para download.


## <a name="object-model"></a>Modelo de objeto

O cliente do QnA Maker é uma [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objeto que autentica para o Azure com Microsoft.Rest.ServiceClientCredentials, que contém a chave.

Depois do cliente é criado, utilize o [base de dados de conhecimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) propriedade criar, gerir e publicar a sua base de dados de conhecimento. 

Gerir a sua base de dados de conhecimento, enviando um objeto JSON. Para operações de imediato, um método normalmente devolve um objeto JSON com a indicação de estado. Para operações de longa execução, a resposta é o ID da operação. Chamar o [cliente. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) método com o ID de operação para determinar a [estado do pedido](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Exemplos de código

Estes trechos de código mostram-lhe como fazer o seguinte com a biblioteca de cliente do QnA Maker para .NET:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Transfira uma base de dados de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter o estado de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra a **Program.cs** ficheiro no seu editor preferencial ou IDE. Substituir o existente `using` código pelo seguinte `using` diretivas:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Na **principal** método, crie uma variável para a chave do recurso do Azure obtida a partir de uma variável de ambiente com o nome `QNAMAKER_SUBSCRIPTION_KEY`. Se tiver criado a variável de ambiente depois da aplicação for iniciada, o editor, o IDE ou a shell de executá-lo precisará ser fechado e recarregado para aceder à variável. Os métodos serão possível criar mais tarde.

Em seguida, crie uma [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave de objeto e utilizá-lo com o ponto final para criar um [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objeto.

Se a chave não está no `westus` região, como este código de exemplo mostra, alterar a localização para o **ponto final** variável. Esta localização se encontra na **descrição geral** página para o seu recurso do QnA Maker no portal do Azure.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objeto de três fontes:

* Para **conteúdo editorial**, utilize o [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objeto.
* Para **arquivos**, utilize o [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objeto. 
* Para **URLs**, utilize uma lista de cadeias de caracteres.

Chamar o [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) método, em seguida, passar o ID de operação retornado para o [MonitorOperation](#get-status-of-an-operation) método para consultar de estado. 

A última linha de código a seguir retorna a ID de base de dados de conhecimento da resposta de MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de dados de conhecimento ao transmitir o ID de base de dados de conhecimento e um [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) que contém [adicione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [atualizar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet), e [eliminar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet)DTO objetos para o [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) método. Utilize o [MonitorOperation](#get-status-of-an-operation) método para determinar se a atualização foi concluída com êxito.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Transfira uma base de dados de conhecimento

Utilize o [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) método para transferir a base de dados como uma lista de [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Isto é _não_ equivalente ao exportar a partir do portal do QnA Maker a **definições** página porque o resultado deste método não é um ficheiro TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publicar a base de dados de conhecimento utilizando a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) método. Isso leva o modelo guardado e treinado atual, referenciado pelo ID de base de dados de conhecimento e publica que num ponto de extremidade. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Eliminar a base de dados com o [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) método com um parâmetro do ID da base de dados de conhecimento. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, tais como criar e atualizar, podem demorar tempo suficiente que em vez de aguardar o processo terminar, uma [operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) é devolvido. Utilize o [ID da operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) da operação para consultar (com a lógica de repetição) para determinar o estado do método original. 

O _loop_ e _Task.Delay_ no bloco de código seguintes são utilizados para simular a lógica de repetição. Isso devem ser substituídos pela sua própria lógica de repetição. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação com o dotnet `run` comando a partir do seu aplicativo.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma subscrição de serviços cognitivos, é possível eliminar o recurso ou grupo de recursos. Eliminar o grupo de recursos também elimina a quaisquer outros recursos associados à mesma.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Tutorial: Criar e responder a um KB](../tutorials/create-publish-query-in-portal.md)

* [O que é a API QnA Maker?](../Overview/overview.md)
* [Editar uma base de dados de conhecimento](../how-to/edit-knowledge-base.md)
* [Obter a análise de utilização](../how-to/get-analytics-knowledge-base.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).