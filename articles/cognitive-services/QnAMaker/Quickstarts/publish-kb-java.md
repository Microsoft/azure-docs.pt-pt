---
title: 'Início rápido: publicar base de dados de conhecimento, REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido baseado em Java REST publica sua base de dados de conhecimento e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou bot de chat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 224afcc4a6301b74ad54064df4b65b7cf447c6c6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721130"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Java

Este guia de início rápido baseado em REST orienta você pela publicação programática da base de dados de conhecimento (KB). A publicação emite a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Este início rápido chama as APIs do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Este exemplo usa o [cliente Apache http](https://hc.apache.org/httpcomponents-client-ga/) de componentes http. Você precisa adicionar as seguintes bibliotecas de cliente HTTP do Apache ao seu projeto: 
    * HttpClient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-Logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **início rápido** para seu recurso no portal do Azure.
* O ID da base de dados de conhecimento (KB) do Criador de FAQ encontrado no URL no parâmetro de cadeia de consulta kbid, conforme mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE] 
> Os arquivos de solução completos estão disponíveis no [repositório **Azure-Samples/cognitiva-Services-qnamaker-Java** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Criar um arquivo Java

Abra VSCode e crie um novo arquivo chamado `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior da `PublishKB.java`, acima da classe, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Criar classe PublishKB com o método Main

Depois das dependências, adicione a seguinte classe:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Adicionar as constantes necessárias

No método **Main** , adicione as constantes necessárias para acessar QnA Maker. Substitua os valores pelos seus próprios.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar solicitação POST para publicar base de dados de conhecimento

Após as constantes necessárias, adicione o seguinte código, que faz uma solicitação HTTPS ao API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Criar e executar o programa

Compile e execute o programa na linha de comando. Ele enviará automaticamente a solicitação para o API de QnA Maker, em seguida, ele será impresso na janela do console.

1. Compilar o arquivo:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Execute o arquivo:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos seguintes

Depois que a base de dados de conhecimento for publicada, você precisará da [URL do ponto de extremidade para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [Referência da API REST para o Criador de FAQ (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
