---
title: 'Início rápido: Publicar base de dados de conhecimento, REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de introdução baseada em REST de Java explica-lhe publicar a sua base de dados de conhecimento que envia a versão mais recente da base de dados de conhecimento testada para um índice da Azure Search dedicado que representa a base de dados de conhecimento publicada. Também cria um ponto final que pode ser chamado na sua aplicação ou chatbot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 4ee622c944c5ccd39331ab395eca7b6ff9692b35
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836074"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Início rápido: Publicar uma base de dados de conhecimento no QnA Maker usando Java

Este guia de introdução baseada em REST orienta-o através de publicação por meio de programação de sua base de dados de conhecimento (KB). A publicação emite a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Este início rápido chama as APIs do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Este exemplo utiliza o Apache [cliente HTTP](https://hc.apache.org/httpcomponents-client-ga/) de componentes de HTTP. Terá de adicionar as seguintes bibliotecas de cliente Apache HTTP a seu projeto: 
    * httpclient 4.5.3.jar
    * httpcore 4.4.6.jar
    * Commons-log-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **início rápido** para seu recurso no portal do Azure.
* O ID da base de dados de conhecimento (KB) do Criador de FAQ encontrado no URL no parâmetro de cadeia de consulta kbid, conforme mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se você ainda não tiver uma base de dados de conhecimento, poderá criar uma amostra a ser usada para este guia de início rápido: [Crie uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE] 
> Os arquivos de solução completos estão disponíveis no [repositório **Azure-Samples/cognitiva-Services-qnamaker-Java** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Crie um ficheiro de Java

Abra o VSCode e crie um novo ficheiro designado `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `PublishKB.java`, acima da classe, adicione as seguintes linhas ao adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Criar a classe PublishKB com o método principal

Depois das dependências, adicione a seguinte classe:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Na **principal** método, adicione as constantes necessárias para acessar o QnA Maker. Substitua os valores pelos seus próprios.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar pedido POST para publicar a base de dados de conhecimento

Depois das constantes necessárias, adicione o seguinte código, o que faz um pedido HTTPS para a API do QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Crie e execute o programa a partir da linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

1. Crie o ficheiro:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Execute o ficheiro:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

Depois da base de dados de conhecimento é publicado, é necessário o [URL de ponto final para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
