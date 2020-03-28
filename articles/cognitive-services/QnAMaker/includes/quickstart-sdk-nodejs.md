---
title: 'Quickstart: Biblioteca de clientes QnA Maker para Node.js'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021229"
---
Utilize a biblioteca de clientes QnA Maker para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave final do ponto final publicada
* Aguarde a tarefa de longo prazo
* Eliminar base de conhecimento

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Pacote de código fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | da biblioteca[(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js.](https://nodejs.org)

## <a name="setting-up"></a>Configuração

### <a name="create-a-qna-maker-azure-resource"></a>Criar um recurso QnA Maker Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Fabricante QnA utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local.

Depois de obter a chave e o ponto final do seu recurso, obtenha os valores do portal Azure, para o seu novo recurso, na página Quickstart.

[Criar variáveis ambientais, nomeadas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `QNAMAKER_AUTHORING_KEY` e. `QNAMAKER_ENDPOINT` Pode copiar o ficheiro [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) para `.env` e utilizar as variáveis ambientais nesse ficheiro.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Executar `npm init -y` o comando para criar uma `package.json` aplicação de nó com um ficheiro.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Instale os pacotes NPM necessários e opcionais:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

O ficheiro `package.json` da sua aplicação é atualizado com as dependências. O `dotenv` é opcional e é usado para permitir definir as variáveis ambientais num ficheiro de texto. Não verifique `.env` o controlo de origem.


## <a name="object-model"></a>Modelo de objeto

O cliente QnA Maker é um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) que autentica o Azure usando serviceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize a propriedade [base do Conhecimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) para criar, gerir e publicar a sua base de conhecimento.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente devolve um estado indicativo do objeto JSON. Para operações de longo prazo, a resposta é o ID de operação. Ligue para o [cliente. Operações.getMétodo DeDetalhes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) com o ID de operação para determinar o [estado do pedido](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes QnA Maker para Node.js:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter ponto final publicado](#get-published-endpoint)
* [Obter o estado de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicione as dependências

Crie um ficheiro com o nome `index.js`. Adicione a biblioteca QnA Maker e as dependências ao ficheiro.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Crie variáveis para o ponto final e chave azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

|Variável de ambiente|Variável Nó.js|Exemplo|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|A chave é uma cadeia de caracteres de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página Quickstart. Isto não é o mesmo que a chave final da previsão.|
|`QNAMAKER_ENDPOINT`|`endpoint`| O seu ponto final de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`autoria, no formato de, inclui o seu nome de **recurso.** Este não é o mesmo URL usado para consultar o ponto final da previsão.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em seguida, crie um objeto ApiKeyCredencia com a sua chave e use-o com o seu ponto final para criar um objeto [QnAMakerClient.](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) Use o objeto do cliente para obter um objeto de cliente base de [conhecimento.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) de três fontes:

* Para **obter conteúdo editorial,** utilize o objeto [QnADTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest)
* Para **ficheiros,** utilize o objeto [FileDTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest)
* Para **URLs,** utilize uma lista de cordas.

Ligue para o método [de criação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) com a informação da base de conhecimento. A informação da base de conhecimento é basicamente um objeto JSON.

Quando o método de criação voltar, passe o ID de operação devolvido para o [método wait_for_operation](#get-status-of-an-operation) para sondar o estado. O método wait_for_operation retorna quando a operação termina. Analise `resourceLocation` o valor cabeçalho da operação devolvida para obter a nova identificação da base de conhecimento.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Certifique-se de [`wait_for_operation`](#get-status-of-an-operation) que a função, referenciada no código acima, para criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos, passando o ID da base de conhecimentos e um [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contendo [adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [atualizar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update), e [eliminar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) objetos dTO ao método de [atualização.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) Os DTOs também são basicamente objetos JSON. Utilize o método [wait_for_operation](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Certifique-se de [`wait_for_operation`](#get-status-of-an-operation) que a função, referenciada no código acima, para atualizar com sucesso uma base de conhecimento.

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimento utilizando o método de [publicação.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) Isto pega no modelo atual guardado e treinado, referenciado pela id base de conhecimento, e publica-o num ponto final. Verifique o código de resposta HTTP para validar a publicação bem sucedida.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Obter ponto final publicado

Uma vez publicada a base de conhecimento, aceda à base de conhecimentos publicada através da previsão de previsão de consulta' geração API. Para isso, precisa da chave final do prazo. Isto é diferente da chave de autor.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Duas chaves de ponto final são devolvidas da chamada. Apenas um é necessário para aceder ao ponto final do tempo de execução.

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimentos utilizando o método [de eliminação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) com um parâmetro do ID da base de conhecimento.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) é devolvida. Utilize o ID de [operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) da operação para a sondagem (com lógica de retry) para determinar o estado do método original.

A chamada _retardada_ no seguinte bloco de código é usada para simular a lógica de retry. Substitua isto com a sua própria lógica de retry.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a `node index.js` aplicação com o comando do seu diretório de candidatura.

Todos os códigos deste artigo estão [disponíveis](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) e podem ser executados como um único ficheiro.

```console
node index.js
```

O programa imprime o estado da consola:

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

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)