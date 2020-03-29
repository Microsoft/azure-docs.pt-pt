---
title: 'Quickstart: Obtenha resposta da base de conhecimento - REST, Java - QnA Maker'
description: Este quickstart baseado em Java REST leva-o através de obter uma resposta de uma base de conhecimento, programáticamente.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 67f09b6d1e284cdf35825a2e584b372bd2adf70a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851742"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento com Java

Este quickstart leva-o programáticamente a obter uma resposta de uma base de conhecimento publicada da QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes](../Concepts/knowledge-base.md) de dados como FAQs. A [questão](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada ao serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais bem prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Esta amostra utiliza o cliente Apache [HTTP](https://hc.apache.org/httpcomponents-client-ga/) dos Componentes HTTP. Você precisa adicionar as seguintes bibliotecas de clientes Apache HTTP ao seu projeto:
    * httpcliente-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Código de estúdio visual](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave, selecione **Keys** sob **Gestão** de Recursos no seu painel Azure para o seu recurso QnA Maker.
* **Publique** as definições da página. Se não tiver uma base de conhecimento publicada, crie uma base de conhecimento vazia e, em seguida, importe uma base de conhecimento na página **Definições** e, em seguida, publique. Pode descarregar e utilizar [esta base de conhecimentos básicos.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    As definições da página de publicação incluem o valor da rota POST, o valor do anfitrião e o valor EndpointKey.

    ![Publish settings (Definições de publicação)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Criar um ficheiro java

Abra o VSCode e `GetAnswer.java` crie um novo ficheiro nomeado e adicione a seguinte classe:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Este quickstart utiliza aulas Apache para pedidos HTTP. Acima da classe GetAnswer, no `GetAnswer.java` topo do ficheiro, adicione as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

No topo da `GetAnswer.java` classe, adicione as constantes necessárias para aceder ao QnA Maker. Estes valores estão na página **Publicar** depois de publicar a base de conhecimento.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Adicione um pedido post para enviar a pergunta

O seguinte código faz um pedido HTTPS à API do Fabricante de QnA para enviar a questão para a base de conhecimento e recebe a resposta:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho `EndpointKey`inclui a corda .

Saiba mais sobre o [pedido](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Construa e execute o programa a partir da linha de comando. Enviará automaticamente o pedido para a API do Fabricante qnA, e depois irá imprimir para a janela da consola.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)