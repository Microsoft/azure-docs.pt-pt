---
title: 'Quickstart: Biblioteca de clientes QnA Maker para .NET'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: ce12b0d5739f3c17a324a663a777b70e61f167d1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204042"
---
Utilize a biblioteca de clientes QnA Maker para .NET para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave final do ponto final publicada
* Aguarde a tarefa de longo prazo
* Eliminar base de conhecimento

[Documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Pacote de código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | da biblioteca[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# Amostras](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* O [Estúdio Visual IDE](https://visualstudio.microsoft.com/vs/) ou versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autor e ponto final. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API do Fabricante qnA. Vaicolar a chave e o ponto final no código abaixo no arranque rápido.
    * Você pode usar o nível de preços gratuitos (`F0`) para experimentar o serviço, e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-qna-maker-azure-resource"></a>Criar um recurso QnA Maker Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Fabricante QnA utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local.

Depois de obter uma chave e ponto final para o `QNAMAKER_SUBSCRIPTION_KEY`seu recurso, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada . O nome do recurso é usado como subdomínio personalizado do URL do ponto final.

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor ou IDE preferido.

Numa janela de consola (como cmd, PowerShell `dotnet new` ou Bash), utilize o comando `qna-maker-quickstart`para criar uma nova aplicação de consola com o nome . Este comando cria um simples projeto C# "Hello World" com um único ficheiro fonte: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```dotnetcli
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

### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório de aplicações, instale a biblioteca de clientes QnA Maker para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.


## <a name="object-model"></a>Modelo de objeto

O cliente QnA Maker é um objeto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) que autentica o Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize a propriedade base do [Conhecimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) para criar, gerir e publicar a sua base de conhecimentos.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente devolve um estado indicativo do objeto JSON. Para operações de longo prazo, a resposta é o ID de operação. Ligue para o [cliente. Operações.ObterOSAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) método com o ID de operação para determinar o [estado do pedido](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).


## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes QnA Maker para .NET:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Descarregue uma base de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter o estado de uma operação](#get-status-of-an-operation)
* [Gerar uma resposta a partir da base de conhecimento](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro **Program.cs** no seu editor ou IDE preferido. Substitua o `using` código existente `using` pelas seguintes diretivas:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimento

No método **principal,** crie uma variável para a chave Azure `QNAMAKER_SUBSCRIPTION_KEY`do seu recurso retirada de uma variável ambiental chamada . Se criou a variável ambiental após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde.

Em seguida, crie um objeto [ApiKeyServiceClientCredenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [QnAMakerClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)

|Variável de ambiente|variável|Exemplo|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|A chave é uma cadeia de caracteres de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página Quickstart. Isto não é o mesmo que a chave final da previsão.|
|`QNAMAKER_HOST`|`Endpoint`| O seu ponto final de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`autoria, no formato de, inclui o seu nome de **recurso.** Este não é o mesmo URL usado para consultar o ponto final da previsão.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

No método **principal,** crie uma variável para a autenticação do `QNAMAKER_ENDPOINT_HOSTNAME` `QNAMAKER_ENDPOINT_KEY`seu recurso retirada de uma variável ambiental chamada e . Quando publica a sua base de conhecimento, estes valores são devolvidos. Depois de publicar, pode encontrar estas definições na página **Definições** do portal QnA Maker.

Crie um [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de aprendizagem ativa.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey&highlight=3)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) de três fontes:

* Para **obter conteúdo editorial,** utilize o objeto [QnADTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)
* Para **ficheiros,** utilize o objeto [FileDTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet)
* Para **URLs,** utilize uma lista de cordas.

Ligue para o método [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) e, em seguida, passe o ID de operação devolvido para o método [MonitorOperation](#get-status-of-an-operation) para fazer sondagem para o estado.

A linha final do código seguinte devolve o ID da base de conhecimento da resposta da MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=30)]

Certifique-se de [`MonitorOperation`](#get-status-of-an-operation) que a função, referenciada no código acima, para criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos através da identificação da base de conhecimentos e de um [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) que contenha [adicionar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [atualizar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)e [eliminar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) objetos DTO ao método [UpdateAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) Utilize o método [MonitorOperation](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Certifique-se de [`MonitorOperation`](#get-status-of-an-operation) que a função, referenciada no código acima, para atualizar com sucesso uma base de conhecimento.

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) para descarregar a base de dados como uma lista de [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimento utilizando o método [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) Isto pega no modelo atual guardado e treinado, referenciado pela id base de conhecimento, e publica-o num ponto final.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [Método GenerateAnswerAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) Este método aceita o ID da base de conhecimento e a [ConsultaDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Aceda a propriedades adicionais da ConsultaDTO, tal como um [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) para usar no seu chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimentos utilizando o método [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) com um parâmetro do ID da base de conhecimento.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) é devolvida. Utilize o ID de [operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) da operação para a sondagem (com lógica de retry) para determinar o estado do método original.

O _loop_ e a _Tarefa.Atraso_ no seguinte bloco de código supor a lógica de retry. Estes devem ser substituídos pela sua própria lógica de reprovação.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a `dotnet run` aplicação com o comando do seu diretório de candidatura.

Todos os códigos deste artigo estão [disponíveis](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) e podem ser executados como um único ficheiro.

```dotnetcli
dotnet run
```

O [código fonte para este quickstart](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) está disponível no repositório GitHub do Fabricante de QnA.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)