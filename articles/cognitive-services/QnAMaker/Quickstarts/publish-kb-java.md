---
title: 'Quickstart: Publicar base de conhecimento, REST, Java - QnA Maker'
description: Este quickstart baseado em Java REST publica a sua base de conhecimentos e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 149d7963f29bf041cda75fffaac533e0a62ee7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851687"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Java

Este quickstart baseado em REST acompanha-o através da publicação programática da sua base de conhecimentos (KB). A publicação empurra a versão mais recente da base de conhecimento para um índice dedicado de Pesquisa Cognitiva Azure e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.

Este início rápido chama as API do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* Esta amostra utiliza o cliente Apache [HTTP](https://hc.apache.org/httpcomponents-client-ga/) dos Componentes HTTP. Você precisa adicionar as seguintes bibliotecas de clientes Apache HTTP ao seu projeto:
    * httpcliente-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Código de estúdio visual](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.
* QnA Maker base de conhecimento (KB) `kbid` ID encontrado no URL no parâmetro de corda de consulta, como mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os ficheiros completos de solução estão disponíveis no [repositório **Azure-Samples/cognitive-services-qnamaker-java** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Criar um ficheiro Java

Abra o VSCode e `PublishKB.java`crie um novo ficheiro chamado .

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

No topo `PublishKB.java`da , acima da classe, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Criar classe PublishKB com método principal

Após as dependências, adicione a seguinte aula:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Adicionar constantes necessárias

No método **principal,** adicione as constantes necessárias para aceder ao QnA Maker. Substitua os valores por si próprio.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar pedido post para publicar base de conhecimento

Após as constantes necessárias, adicione o seguinte código, que faz um pedido HTTPS à API do Fabricante de QnA para publicar uma base de conhecimento e recebe a resposta:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Construa e execute o programa a partir da linha de comando. Enviará automaticamente o pedido para a API do Fabricante qnA, e depois irá imprimir para a janela da consola.

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

Após a publicação da base de conhecimentos, é necessário que o [URL do ponto final gere uma resposta](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)