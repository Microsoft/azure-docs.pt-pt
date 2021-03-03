---
title: 'Quickstart: Biblioteca de clientes QnA Maker para Java'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 7c14391ca565105809666362a1c452d910140c64
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730999"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Utilize a biblioteca de clientes QnA Maker para:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Obtenha a chave final do tempo de execução da previsão
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obtenha uma resposta de uma base de conhecimento
* Eliminar base de conhecimento

[Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker)  |  [Pacote](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Utilize a biblioteca de clientes QnA Maker para:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obtenha uma resposta de uma base de conhecimento
* Eliminar base de conhecimento

[Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker)  |  [Pacote](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final.
    * NOTA: Certifique-se de selecionar a caixa de verificação **Gerida.**
    * Depois de o seu recurso QnA Maker ser implementado, selecione **Ir para o recurso**. Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-libraries"></a>Instalar as bibliotecas do cliente

Depois de instalar a Java, pode instalar as bibliotecas do cliente utilizando o [Maven](https://maven.apache.org/) do [Repositório MVN.](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker)

### <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Crie um novo ficheiro com o nome `quickstart.java` e importe as seguintes bibliotecas.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

Crie variáveis para o ponto final e chave Azure do seu recurso.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**

- Crie variáveis ambientais denominadas QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT e QNA_MAKER_RUNTIME_ENDPOINT para armazenar estes valores.
- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. 
- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.azurewebsites.net` formato.
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**

- Crie variáveis ambientais denominadas QNA_MAKER_SUBSCRIPTION_KEY e QNA_MAKER_ENDPOINT para armazenar estes valores.
- O valor de QNA_MAKER_ENDPOINT tem o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` formato. 
- Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>Modelos de objetos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O Fabricante QnA usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../how-to/use-active-learning.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

O Fabricante QnA utiliza o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar, transferir e consultar a base de conhecimentos.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da Autoria QnA Maker é um objeto [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) que autentica a Azure usando msRest::ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize os métodos da propriedade [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) do cliente para criar, gerir e publicar a sua base de conhecimentos.

Para operações imediatas, um método normalmente devolve o resultado, se houver. Para operações de longa duração, a resposta é um objeto [de Operação.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) Ligue para o método [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) com o `operation.operationId` valor para determinar o estado do [pedido](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O cliente QnA Maker é um objeto [QnAMakerRuntimeClient.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)

Depois de publicar a sua base de conhecimento utilizando o cliente autoria, utilize o método [de geração](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) do cliente em tempo de execução Para obter uma resposta da base de conhecimento.

Cria um cliente em tempo de execução ligando para [qnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) e passando uma chave de ponto final de tempo de execução. Para obter a chave de ponto final de tempo de execução, utilize o cliente de autoria para ligar para [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Um recurso gerido pelo QnA Maker não requer a utilização do objeto QnAMakerRuntimeClient. Em vez disso, [chama-se generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) diretamente no objeto [QnAMakerClient.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimentos

Instantiizar um cliente com a sua chave de fim de autor e subscrição.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

Ligue para o método [de criação](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) e, em seguida, passe a `operationId` propriedade da operação devolvida para o método [getDetails](#get-status-of-an-operation) para sondar o estado.

A linha final do seguinte código devolve o ID da base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimento, chamando a [atualização](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) e passando no ID da base de conhecimento e num objeto [UpdateKbOperationDTO.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java) O objeto, por sua vez, pode conter:
- [adicionar](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [atualizar](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Passe a `operationId` propriedade da operação devolvida para o método [getDetails](#get-status-of-an-operation) para sondar o estado.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [de descarregamento](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) para descarregar a base de dados como uma lista de [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos utilizando o método [de publicação.](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) Isto pega no modelo atual guardado e treinado, referenciado pela base de conhecimento ID, e publica-o num ponto final.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

Uma vez publicada uma base de conhecimento, é necessário a chave final do ponto de execução para consultar a base de conhecimento. Isto não é o mesmo que a chave de subscrição utilizada para criar o cliente autoria.

Utilize o método [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) para obter um objeto [EndpointKeysDTO.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java)

Crie um cliente em tempo de execução ligando para [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) e passando uma chave de ponto final de tempo de execução a partir do objeto EndpointKeysDTO.

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método [generateAnswer.](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) Este método aceita o ID da base de conhecimento e um objeto [QueryDTO.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java)

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método [generateAnswer.](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) Este método aceita o ID da base de conhecimento e um objeto [QueryDTO.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java)

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

Este é um simples exemplo de consulta de uma base de conhecimento. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimento utilizando o método [de eliminação](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) com um parâmetro do ID da base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) seja devolvida. Utilize o ID de funcionamento da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>Executar a aplicação

Aqui está o método principal para a aplicação.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

Executar a aplicação da seguinte forma. Isto pressupõe que o seu nome de classe é `Quickstart` e as suas dependências estão numa sub-página chamada `lib` abaixo da pasta atual.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java).

---
