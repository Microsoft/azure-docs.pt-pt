---
title: 'Quickstart: Biblioteca de clientes QnA Maker para Node.js'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0737d0550df6d8dd2e3bb61bb71188479d87363e
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105479"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Utilize a biblioteca de clientes QnA Maker para Node.js:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Obtenha a chave final do tempo de execução da previsão
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obtenha uma resposta de uma base de conhecimento
* Eliminar base de conhecimento

[Documentação de referência](/javascript/api/@azure/cognitiveservices-qnamaker/)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [amostras deNode.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Utilize a biblioteca de clientes QnA Maker para Node.js:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obtenha uma resposta de uma base de conhecimento
* Eliminar base de conhecimento

[Documentação de referência](/javascript/api/@azure/cognitiveservices-qnamaker/)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [amostras deNode.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js. ](https://nodejs.org)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e recurso. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e nome de recurso do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Você vai colar a sua chave e nome de recurso no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js. ](https://nodejs.org)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final.
    * NOTA: Certifique-se de selecionar a caixa de verificação **Gerida.**
    * Depois de o seu recurso QnA Maker ser implementado, selecione **Ir para o recurso**. Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Executar o `npm init -y` comando para criar uma aplicação de nó com um `package.json` ficheiro.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Instale os seguintes pacotes NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Instale os seguintes pacotes NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

O ficheiro da sua aplicação `package.json` é atualizado com as dependências.

Criar um ficheiro denominado index.js e importar as seguintes bibliotecas:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Crie uma variável para a chave Esta do seu recurso e nome de recurso.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**

- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. Vá ao recurso QnA Maker no portal Azure e clique em **Keys e Endpoint** para localizar a tecla de autoria (subscrição) e o Ponto Final do Criador QnA.

 ![QnA Maker Authoring Endpoint](../media/keys-endpoint.png)
 
- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.azurewebsites.net` formato. 
   
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**

- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. Vá ao recurso QnA Maker no portal Azure e clique em **Keys e Endpoint** para localizar a tecla de autoria (subscrição) e o Ponto Final do Criador QnA.

 ![QnA Maker Authoring Endpoint](../media/keys-endpoint.png)
 
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Modelos de objetos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[O Fabricante QnA](/javascript/api/@azure/cognitiveservices-qnamaker/) usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../how-to/use-active-learning.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[O Fabricante QnA](/javascript/api/@azure/cognitiveservices-qnamaker/) utiliza o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar, transferir e consultar a base de conhecimentos.

---

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da Autoria QnA Maker é um objeto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) que autentica a Azure usando as suas credenciais, que contém a sua chave.

Assim que o cliente for criado, utilize a [base de conhecimentos](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient#knowledgebase) para criar, gerir e publicar a sua base de conhecimentos.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método normalmente devolve um objeto JSON indicando o estado. Para operações de longa duração, a resposta é a identificação da operação. Ligue para o [método client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations#getdetails-string--msrest-requestoptionsbase-) com o ID de operação para determinar o [estado do pedido](/javascript/api/@azure/cognitiveservices-qnamaker/operation).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O cliente QnA Maker de previsão é um objeto QnAMakerRuntimeClient que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave de tempo de previsão, devolvida a partir da chamada do cliente autoria, [cliente. EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys#getkeys-msrest-requestoptionsbase-) após a publicação da base de conhecimentos.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Um recurso gerido pelo QnA Maker não requer a utilização do objeto QnAMakerRuntimeClient. Em vez disso, [chama-se generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) diretamente no objeto [QnAMakerClient.](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient)

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

Instantiar um cliente com o seu ponto final e chave. Crie um objeto ServiceClientCredentials com a sua chave e use-o com o seu ponto final para criar um objeto [QnAMakerClient.](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient)

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](/javascript/api/@azure/cognitiveservices-qnamaker/filedto) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

O passo de criação também inclui propriedades para a base de conhecimentos:
* `defaultAnswerUsedForExtraction` - o que é devolvido quando não se encontra uma resposta
* `enableHierarchicalExtraction` - criar automaticamente relações rápidas entre pares QnA extraídos
* `language` - ao criar a primeira base de conhecimentos de um recurso, desaveja o idioma a utilizar no índice de Pesquisa de Azure.

Ligue para o método [de criação](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#create-createkbdto--servicecallback-operation--) com a informação da base de conhecimento. A informação da base de conhecimento é basicamente um objeto JSON.

Quando o método de criação voltar, passe o ID de operação devolvido para o método [wait_for_operation](#get-status-of-an-operation) para sondar o estado. O método wait_for_operation regressa quando a operação estiver concluída. Analise o `resourceLocation` valor do cabeçalho da operação devolvida para obter o novo ID da base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Certifique-se de incluir a [`wait_for_operation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos transmitindo o ID da base de conhecimento e um [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto) contendo [adicionar,](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#add) [atualizar](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#update)e [eliminar](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#deleteproperty) objetos DTO para o método de [atualização.](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#update-string--updatekboperationdto--msrest-requestoptionsbase-) Os DTOs também são basicamente objetos JSON. Utilize o método [wait_for_operation](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Certifique-se de que inclui a [`wait_for_operation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a atualizar com sucesso uma base de conhecimento.

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [de descarregamento](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#download-string--models-environmenttype--msrest-requestoptionsbase-) para descarregar a base de dados como uma lista de [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos utilizando o método [de publicação.](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#publish-string--msrest-requestoptionsbase-) Isto pega no modelo atual guardado e treinado, referenciado pela base de conhecimento ID, e publica-o num ponto final. Consulte o código de resposta HTTP para validar que a publicação foi bem sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Consultar uma base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Obtenha chave de tempo de execução de consulta

Uma vez publicada uma base de conhecimento, precisa da chave de tempo de consulta para consultar o tempo de execução. Esta não é a mesma chave usada para criar o objeto cliente original.

Utilize o método [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys) para obter a classe [EndpointKeysDTO.](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto)

Utilize qualquer uma das propriedades chave devolvidas no objeto para consultar a base de conhecimentos.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

Crie um QnAMakerRuntimeClient para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de uma aprendizagem ativa.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Utilize o QnAMakerRuntimeClient para obter uma resposta do conhecimento ou para enviar novas perguntas sugeridas para a base de conhecimento para [a aprendizagem ativa.](../concepts/active-learning-suggestions.md)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método RuntimeClient.runtime.generateAnswer. Este método aceita o ID da base de conhecimento e o [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Aceda a propriedades adicionais do QueryDTO, tal Top e Context para usar no seu chat bot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método QnAMakerClient.knowledgebase.generateAnswer. Este método aceita o ID da base de conhecimento e o [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Aceda a propriedades adicionais do QueryDTO, tal Top e Context para usar no seu chat bot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Este é um exemplo simples consultando a base de conhecimento. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimento utilizando o método [de eliminação](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#deletemethod-string--msrest-requestoptionsbase-) com um parâmetro do ID da base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](/javascript/api/@azure/cognitiveservices-qnamaker/operations) seja devolvida. Utilize o ID de [funcionamento](/javascript/api/@azure/cognitiveservices-qnamaker/operation#operationid) da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

A chamada _delayTimer_ no bloco de código que se segue é utilizada para simular a lógica de retrip. Substitua-o pela sua própria lógica de repetição.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com `node index.js` o comando do seu diretório de candidaturas.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js).

---
