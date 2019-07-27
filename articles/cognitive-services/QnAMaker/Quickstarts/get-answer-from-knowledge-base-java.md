---
title: 'Início rápido: Obter resposta da base de dados de conhecimento-REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido baseado em REST Java orienta você pela obtenção de uma resposta de uma base de dados de conhecimento, programaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 2b888a275b08c7011c6e0b60ff1cd1d70b42f465
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559835"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Obtenha respostas para uma pergunta de uma base de dados de conhecimento com Java

Este guia de início rápido orienta você na obtenção de uma resposta de uma base de dados de conhecimento QnA Maker publicada. A base de conhecimento contém perguntas e respostas de [fontes de dados](../Concepts/data-sources-supported.md) , como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço de QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista. 

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Este exemplo utiliza o Apache [cliente HTTP](https://hc.apache.org/httpcomponents-client-ga/) de componentes de HTTP. Terá de adicionar as seguintes bibliotecas de cliente Apache HTTP a seu projeto: 
    * httpclient 4.5.3.jar
    * httpcore 4.4.6.jar
    * Commons-log-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** configurações da página. Se você não tiver uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe uma base de dados de conhecimento na página **configurações** e, em seguida, publique. Você pode baixar e usar [essa base de dados de conhecimento básica](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As configurações da página de publicação incluem o valor da rota de POST, o valor do host e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este guia de início rápido [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer) está no repositório. 

## <a name="create-a-java-file"></a>Criar um arquivo Java

Abra VSCode e crie um novo arquivo chamado `GetAnswer.java` e adicione a seguinte classe:

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Este guia de início rápido usa classes Apache para solicitações HTTP. Acima da classe getanswer, na parte superior do `GetAnswer.java` arquivo, adicione as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da `GetAnswer.java` classe, adicione as constantes necessárias para acessar QnA Maker. Esses valores estão na página **publicar** depois que você publicar a base de dados de conhecimento.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Adicionar uma solicitação POST para enviar a pergunta

O código a seguir faz uma solicitação HTTPS para o API de QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia `EndpointKey`de caracteres. 

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Crie e execute o programa a partir da linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

1. Crie o ficheiro:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Execute o ficheiro:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
