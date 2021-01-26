---
title: 'Quickstart: Biblioteca de clientes QnA Maker para Python'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 70d55bd6fba209119efa0d3828cad91c2de09f82
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792410"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Utilize a biblioteca cliente do QnA Maker para: python para:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Obtenha a chave final do tempo de execução da previsão
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obtenha uma resposta de uma base de conhecimento
* Eliminar base de conhecimento

[Documentação de referência](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/)  |  [Amostras de python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Utilize a biblioteca cliente do QnA Maker para: python para:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obtenha uma resposta de uma base de conhecimento
* Eliminar base de conhecimento

[Documentação de referência](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/)  |  [Amostras de python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final.
    * NOTA: Certifique-se de selecionar a caixa de verificação **Gerida.**
    * Depois de o seu recurso QnA Maker ser implementado, selecione **Ir para o recurso**. Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python nomeado `quickstart-file.py` e importe as seguintes bibliotecas.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Crie variáveis para o ponto final e chave Azure do seu recurso.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**

- Crie variáveis ambientais denominadas QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT e QNA_MAKER_RUNTIME_ENDPOINT para armazenar estes valores.
- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. 
- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.azurewebsites.net` formato.
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**

- Crie variáveis ambientais denominadas QNA_MAKER_SUBSCRIPTION_KEY e QNA_MAKER_ENDPOINT para armazenar estes valores.
- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. 
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Modelos de objetos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[O Fabricante QnA](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../concepts/active-learning-suggestions.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[O Fabricante QnA](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) utiliza o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar, transferir e consultar a base de conhecimentos.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da autoria do QnA Maker é um objeto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python) que autentica a Azure usando microsoft.rest.ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize a propriedade [Base de Conhecimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) para criar, gerir e publicar a sua base de conhecimentos.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método normalmente devolve um objeto JSON indicando o estado. Para operações de longa duração, a resposta é a identificação da operação. Ligue para o [método operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#get-details-kb-id--custom-headers-none--raw-false----operation-config-) com o ID de operação para determinar o estado [do pedido](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O cliente QnA Maker de previsão é um `QnAMakerRuntimeClient` objeto que autentica para o Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave de tempo de previsão, devolvida a partir da chamada do cliente de autoria, [cliente. EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) depois da base de conhecimentos ser publicada.

Utilize o `generate_answer` método para obter uma resposta a partir do tempo de funcionação da consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Um recurso gerido pelo QnA Maker não requer a utilização do objeto QnAMakerRuntimeClient. Em vez disso, ligue para [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) diretamente no objeto [QnAMakerClient.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimentos

Instantiar um cliente com o seu ponto final e chave. Crie um objeto CognitiveServicesCredentials com a sua chave e use-o com o seu ponto final para criar um objeto [QnAMakerClient.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python)

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Use o objeto do cliente para obter um objeto [de operações de base de conhecimento.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python)

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

Ligue para o método [de criação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) e, em seguida, passe o ID de operação devolvido para o método [Operations.getDetails](#get-status-of-an-operation) para sondar o estado.

A linha final do seguinte código devolve o ID da base de conhecimento da resposta da MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

Certifique-se de que a [`_monitor_operation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos transmitindo o ID da base de conhecimento e um [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) contendo [adicionar,](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python) [atualizar](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python)e [eliminar](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) objetos DTO para o método de [atualização.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) Utilize o método [Operation.getDetail](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Certifique-se de que inclui a [`_monitor_operation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a atualizar com sucesso uma base de conhecimento.

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [de descarregamento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) para descarregar a base de dados como uma lista de [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos utilizando o método [de publicação.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) Isto pega no modelo atual guardado e treinado, referenciado pela base de conhecimento ID, e publica-o num ponto final.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Consultar uma base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Obtenha chave de tempo de execução de consulta

Uma vez publicada uma base de conhecimento, precisa da chave de tempo de consulta para consultar o tempo de execução. Esta não é a mesma chave usada para criar o objeto cliente original.

Utilize o método [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) para obter a classe [EndpointKeysDTO.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python)

Utilize qualquer uma das propriedades chave devolvidas no objeto para consultar a base de conhecimentos.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de uma aprendizagem ativa.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Utilize o QnAMakerRuntimeClient para obter uma resposta do conhecimento ou para enviar novas perguntas sugeridas para a base de conhecimento para [a aprendizagem ativa.](../concepts/active-learning-suggestions.md)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método QnAMakerRuntimeClient.runtime.generate_answer. Este método aceita o ID da base de conhecimento e o [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python). Aceda a propriedades adicionais do QueryDTO, tal Top e Context para usar no seu chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método [generate_answer.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) Este método aceita o ID da base de conhecimento e o [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python). Aceda a propriedades adicionais do QueryDTO, tal Top e Context para usar no seu chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Este é um simples exemplo de consulta da base de conhecimento. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimento utilizando o método [de eliminação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) com um parâmetro do ID da base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python) seja devolvida. Utilize o ID de funcionamento da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

A chamada _setTimeout_ no bloco de códigos seguinte é usada para simular código assíncronos. Substitua-o por uma lógica de re-tentar.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o comando python no seu ficheiro quickstart.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).

---