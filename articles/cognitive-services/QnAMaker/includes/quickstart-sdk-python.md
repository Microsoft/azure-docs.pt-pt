---
title: 'Quickstart: Biblioteca de clientes QnA Maker para Python'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 323f0eae3e1ba3d4045f237a819bc839cf298821
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303643"
---
Utilize a biblioteca cliente do QnA Maker para: python para:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Obtenha a chave final do tempo de execução da previsão
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obter resposta
* Eliminar base de conhecimento

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/)  |  [Amostras de python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python nomeado `quickstart-file.py` e importe as seguintes bibliotecas.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

Crie variáveis para o ponto final e chave Azure do seu recurso.

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**
> Precisa de toda a chave para criar a sua base de conhecimentos. Só precisa do nome de recurso do ponto final. O formato é `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview) fornece armazenamento de chaves seguros.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>Modelos de objetos

[Fabricante QnA](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) O fabricante usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../concepts/active-learning-suggestions.md)

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da autoria do QnA Maker é um objeto [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) que autentica a Azure usando microsoft.rest.ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize a propriedade [Base de Conhecimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python) para criar, gerir e publicar a sua base de conhecimentos.

Gerencie a sua base de conhecimento enviando um objeto JSON. Para operações imediatas, um método normalmente devolve um objeto JSON indicando o estado. Para operações de longa duração, a resposta é a identificação da operação. Ligue para o [método operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) com o ID de operação para determinar o estado [do pedido](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

O cliente QnA Maker de previsão é um objeto [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave de tempo de previsão, devolvida a partir da chamada do cliente autoria, [cliente. EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python) depois da base de conhecimentos ser publicada.

Utilize o método [generate_answer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Runtime__ctor_Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerRuntimeClient_#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) para obter uma resposta a partir do tempo de funcionação da consulta.


## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimentos

Instantiar um cliente com o seu ponto final e chave. Crie um objeto CognitiveServicesCredentials com a sua chave e use-o com o seu ponto final para criar um objeto [QnAMakerClient.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

 Use o objeto do cliente para obter um objeto [de operações de base de conhecimento.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python)

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.create_kb_dto) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.file_dto) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

Ligue para o método [de criação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) e, em seguida, passe o ID de operação devolvido para o método [Operations.getDetails](#get-status-of-an-operation) para sondar o estado.

A linha final do seguinte código devolve o ID da base de conhecimento da resposta da MonitorOperation.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

Certifique-se de que a [`_monitor_operation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a criar com sucesso uma base de conhecimento.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos transmitindo o ID da base de conhecimento e um [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) contendo [adicionar,](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python) [atualizar](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python)e [eliminar](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) objetos DTO para o método de [atualização.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) Utilize o método [Operation.getDetail](#get-status-of-an-operation) para determinar se a atualização foi bem sucedida.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

Certifique-se de que inclui a [`_monitor_operation`](#get-status-of-an-operation) função, referenciada no código acima, de modo a atualizar com sucesso uma base de conhecimento.

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [de descarregamento](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) para descarregar a base de dados como uma lista de [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro TSV.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos utilizando o método [de publicação.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) Isto pega no modelo atual guardado e treinado, referenciado pela base de conhecimento ID, e publica-o num ponto final.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## <a name="get-query-runtime-key"></a>Obtenha chave de tempo de execução de consulta

Uma vez publicada uma base de conhecimento, precisa da chave de tempo de consulta para consultar o tempo de execução. Esta não é a mesma chave usada para criar o objeto cliente original.

Utilize o método [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python) para obter a classe [EndpointKeysDTO.](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python)

Utilize qualquer uma das propriedades chave devolvidas no objeto para consultar a base de conhecimentos.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de uma aprendizagem ativa.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Utilize o QnAMakerRuntimeClient para obter uma resposta do conhecimento ou para enviar novas perguntas sugeridas para a base de conhecimento para [a aprendizagem ativa.](../concepts/active-learning-suggestions.md)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método RuntimeClient.runtime.generateAnswer. Este método aceita o ID da base de conhecimento e o QueryDTO. Aceda a propriedades adicionais do QueryDTO, tal Top e Context para usar no seu chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

Este é um exemplo simples consultando a base de conhecimento. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimento utilizando o método [de eliminação](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) com um parâmetro do ID da base de conhecimento.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python) seja devolvida. Utilize o ID de funcionamento da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

A chamada _setTimeout_ no bloco de códigos seguinte é usada para simular código assíncronos. Substitua-o por uma lógica de re-tentar.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o comando python no seu ficheiro quickstart.

```console
python quickstart-file.py
```

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).