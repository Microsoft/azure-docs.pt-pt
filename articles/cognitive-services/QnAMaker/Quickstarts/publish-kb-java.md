---
title: 'Quickstart: Publicar base de conhecimento, REST, Java - QnA Maker'
description: Este quickstart baseado em Java REST publica a sua base de conhecimento e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 34184123a3fc096c014dc0185b51b088c59a6e04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777518"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Java

Este quickstart baseado em REST acompanha-o através da publicação programática da sua base de conhecimento (KB). A publicação empurra a versão mais recente da base de conhecimento para um índice dedicado de Pesquisa Cognitiva Azure e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.

Este início rápido chama as API do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Esta amostra utiliza o cliente Apache [HTTP](https://hc.apache.org/httpcomponents-client-ga/) a partir de componentes HTTP. Tem de adicionar as seguintes bibliotecas de clientes Apache HTTP ao seu projeto:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.
* QnA Maker base de conhecimento (KB) ID encontrado no URL no parâmetro da `kbid` cadeia de consulta, como mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os ficheiros de solução completos estão disponíveis no [repositório **Azure-Samples/cognitive-services-qnamaker-java** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Criar um ficheiro Java

Abra o VSCode e crie um novo ficheiro chamado `PublishKB.java` .

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

No topo da `PublishKB.java` classificação, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="dependencies":::

## <a name="create-publishkb-class-with-main-method"></a>Criar a classe PublishKB com o método principal

Após as dependências, adicione a seguinte classe:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Adicionar constantes necessárias

No método **principal,** adicione as constantes necessárias para aceder ao QnA Maker. Substitua os valores pelos seus.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar pedido DE POST para publicar base de conhecimento

Após as constantes necessárias, adicione o seguinte código, que faz um pedido HTTPS à API do Fabricante QnA para publicar uma base de conhecimento e recebe a resposta:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="post":::

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Construa e execute o programa a partir da linha de comando. Enviará automaticamente o pedido para a API do Criador QnA, e depois imprimirá na janela da consola.

1. Construa o ficheiro:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Executar o ficheiro:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Após a publicação da base de conhecimento, precisa do URL do [ponto final para gerar uma resposta.](./get-answer-from-knowledge-base-java.md)

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
