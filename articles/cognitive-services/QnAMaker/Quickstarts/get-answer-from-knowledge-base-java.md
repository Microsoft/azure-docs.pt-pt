---
title: 'Quickstart: Obtenha resposta da base de conhecimento - REST, Java - QnA Maker'
description: Este quickstart baseado em Java REST acompanha-o através de obter uma resposta de uma base de conhecimento, programáticamente.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: f162276044e63c9deb46a60033ffe9904366f777
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342804"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento com Java

Este quickstart acompanha-o programáticamente recebendo uma resposta de uma base de conhecimento da QnA Maker publicada. A base de conhecimento contém perguntas e respostas de fontes de [dados,](../Concepts/knowledge-base.md) tais como PERGUNTAS Frequentes. A [questão](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Esta amostra utiliza o cliente Apache [HTTP](https://hc.apache.org/httpcomponents-client-ga/) a partir de componentes HTTP. Tem de adicionar as seguintes bibliotecas de clientes Apache HTTP ao seu projeto:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave, selecione **Keys** sob **Gestão de Recursos** no seu painel Azure para o seu recurso QnA Maker.
* **Publicar** definições de página. Se não tiver uma base de conhecimento publicada, crie uma base de conhecimento vazia e, em seguida, importe uma base de conhecimento na página **Definições** e, em seguida, publique. Você pode baixar e usar [esta base de conhecimento básico.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    As definições da página de publicação incluem o valor da rota POST, o valor do anfitrião e o valor endpointKey.

    ![Publish settings (Definições de publicação)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Criar um ficheiro java

Abra o VSCode e crie um novo ficheiro nomeado `GetAnswer.java` e adicione a seguinte classe:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Este quickstart utiliza aulas Apache para pedidos HTTP. Acima da classe GetAnswer, no topo do `GetAnswer.java` ficheiro, adicione as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

No topo da `GetAnswer.java` classe, adicione as constantes necessárias para aceder ao QnA Maker. Estes valores estão na página **publicar** depois de publicar a base de conhecimentos.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Adicione um pedido de ENVIO para enviar pergunta

O seguinte código faz um pedido HTTPS à API do Fabricante QNA para enviar a pergunta para a base de conhecimento e recebe a resposta:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a `EndpointKey` corda.

Saiba mais sobre o [pedido](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Construa e execute o programa a partir da linha de comando. Enviará automaticamente o pedido para a API do Criador QnA, e depois imprimirá na janela da consola.

1. Construa o ficheiro:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Executar o ficheiro:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)