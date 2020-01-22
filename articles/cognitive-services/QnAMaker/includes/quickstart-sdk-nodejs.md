---
title: 'Início rápido: biblioteca de cliente QnA Maker para node. js'
description: Este guia de início rápido mostra como começar a usar a biblioteca de cliente do QnA Maker para node. js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021229"
---
Use a biblioteca de cliente QnA Maker para node. js para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter chave do ponto de extremidade publicado
* Aguardar tarefa de execução longa
* Excluir base de dados de conhecimento

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [código-fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [do NuGet ( do pacote)de](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [amostras biblioteca](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org).

## <a name="setting-up"></a>Configurando

### <a name="create-a-qna-maker-azure-resource"></a>Criar um QnA Maker recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para QnA Maker usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local.

Depois de obter a chave e o ponto de extremidade do recurso, obtenha os valores da portal do Azure, para o novo recurso, na página início rápido.

[Crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication), chamadas `QNAMAKER_AUTHORING_KEY` e `QNAMAKER_ENDPOINT`. Você pode copiar o arquivo [. env. Sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) para `.env` e usar as variáveis de ambiente nesse arquivo.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Execute o comando `npm init -y` para criar um aplicativo de nó com um arquivo de `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os pacotes NPM necessários e opcionais:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

O arquivo de `package.json` do seu aplicativo é atualizado com as dependências. O `dotenv` é opcional e é usado para permitir que você defina as variáveis de ambiente em um arquivo de texto. Não verifique o `.env` no controle do código-fonte.


## <a name="object-model"></a>Modelo de objeto

O QnA Maker Client é um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) que se autentica no Azure usando o imclientcredentials, que contém sua chave.

Depois que o cliente for criado, use a propriedade [da base de dados de conhecimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chamar o [cliente. Método Operations. GetDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) com a ID da operação para determinar o [status da solicitação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente QnA Maker para node. js:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter ponto de extremidade publicado](#get-published-endpoint)
* [Obter o status de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicionar as dependências

Crie um ficheiro com o nome `index.js`. Adicione a biblioteca de QnA Maker e as dependências ao arquivo.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

|Variável de ambiente|Variável node. js|Exemplo|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|A chave é uma cadeia de caracteres de 32 caracteres e está disponível no portal do Azure, no recurso QnA Maker, na página início rápido. Isso não é o mesmo que a chave de ponto de extremidade de previsão.|
|`QNAMAKER_ENDPOINT`|`endpoint`| O ponto de extremidade de criação, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, inclui o **nome do recurso**. Essa não é a mesma URL usada para consultar o ponto de extremidade de previsão.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em seguida, crie um objeto ApiKeyCredentials com sua chave e use-o com seu ponto de extremidade para criar um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) . Use o objeto de cliente para obter um objeto de [cliente da base de dados de conhecimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) .


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
* Para **arquivos**, use o objeto [encampoto](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) .
* Para **URLs**, use uma lista de cadeias de caracteres.

Chame o método [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) com as informações da base de dados de conhecimento. As informações da base de dados de conhecimento são basicamente um objeto JSON.

Quando o método Create retorna, passe a ID da operação retornada para o método [wait_for_operation](#get-status-of-an-operation) para sondar o status. O método wait_for_operation retorna quando a operação é concluída. Analise o valor do cabeçalho de `resourceLocation` da operação retornada para obter a nova ID da base de dados de conhecimento.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Certifique-se de incluir a função [`wait_for_operation`](#get-status-of-an-operation) , referenciada no código acima, para criar com êxito uma base de dados de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Você pode atualizar uma base de dados de conhecimento passando a ID da base de dados de conhecimento e um [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contendo [Adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Atualizar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)e [excluir](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) objetos dto para o método [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) . Os DTOs também são basicamente objetos JSON. Use o método [wait_for_operation](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Certifique-se de incluir a função [`wait_for_operation`](#get-status-of-an-operation) , referenciada no código acima, para atualizar com êxito uma base de dados de conhecimento.

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Isso usa o modelo atual salvo e treinado, referenciado pela ID da base de dados de conhecimento e o publica em um ponto de extremidade. Verifique o código de resposta HTTP para validar se a publicação foi bem-sucedida.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Obter ponto de extremidade publicado

Depois que a base de dados de conhecimento for publicada, acesse a base de dados de conhecimento publicada por meio da API generateAnswer do tempo de execução de previsão de consulta. Para fazer isso, você precisa da chave do ponto de extremidade do tempo de execução. Isso é diferente da chave de criação.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Duas chaves de ponto de extremidade são retornadas da chamada. Apenas um é necessário para acessar o ponto de extremidade do tempo de execução.

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) com um parâmetro da ID da base de dados de conhecimento.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente que, em vez de aguardar a conclusão do processo, uma [operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) é retornada. Use a [ID da operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) da operação para sondagem (com lógica de repetição) para determinar o status do método original.

A chamada _delayTimer_ no bloco de código a seguir é usada para simular a lógica de repetição. Substitua isso pela sua própria lógica de repetição.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com `node index.js` comando do diretório do aplicativo.

Todos os trechos de código neste artigo estão [disponíveis](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) e podem ser executados como um único arquivo.

```console
node index.js
```

O programa imprime o status no console:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)