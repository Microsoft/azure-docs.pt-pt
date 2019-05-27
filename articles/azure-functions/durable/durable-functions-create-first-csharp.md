---
title: Criar a sua primeira função durável no Azure comC#
description: Crie e publique uma função de durável do Azure com o Visual Studio.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0288d9c0932d012bc83f23053b661c5a7ea2ef82
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872957"
---
# <a name="create-your-first-durable-function-in-c"></a>Criar a sua primeira função durável em C\#

*Funções duráveis* é uma extensão da [as funções do Azure](../functions-overview.md) que permite que escreva funções com monitoração de estado num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

Neste artigo, irá aprender a utilizar as ferramentas de 2019 do Visual Studio para as funções do Azure para criar localmente e testar uma função de durável "hello world".  Orquestra a essa função e em conjunto de cadeias de chamadas para outras funções. Em seguida, publique o código de função no Azure. Estas ferramentas estão disponíveis como parte da carga de trabalho de desenvolvimento do Azure no Visual Studio 2019.

![Executar a função durável no Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale [Visual Studio 2019](https://azure.microsoft.com/downloads/). Certifique-se de que o **desenvolvimento do Azure** carga de trabalho também é instalada.

* Verifique se tem as [ferramentas de Funções do Azure mais recentes](../functions-develop-vs.md#check-your-tools-version).

* Certifique-se de que tem o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) instalado e em execução.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

O modelo de funções do Azure cria um projeto que pode ser publicado para uma aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. No Visual Studio, selecione **Novo** > **Projeto**, no menu **Ficheiro**.

2. Na caixa de diálogo **Novo Projeto**, selecione **Instalado**, expanda **Visual C#** > **Cloud**, selecione **Funções do Azure**, escreva um **Nome** para o projeto e clique em **OK**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

    ![Caixa de diálogo Novo projeto para criar uma função no Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

3. Utilize as definições especificadas na tabela a seguir à imagem.

    ![Caixa de diálogo de função nova no Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Definição      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Funções do Azure 2.x <br />(.NET Core) | Cria um projeto de função que utiliza a versão 2.x do runtime das funções do Azure, que suporta o .NET Core. As Funções do Azure 1.x suportam o .NET Framework. Para obter mais informações, veja [How to target Azure Functions runtime version](../functions-versions.md) (Como segmentar a versão do runtime das Funções do Azure).   |
    | **Modelo** | Vazio | Cria uma aplicação de função vazia. |
    | **Conta de armazenamento**  | Emulador do Armazenamento | Uma conta de armazenamento é necessária para a gestão de estado de função durável. |

4. Clique em **OK** para criar um projeto de função vazia. Este projeto tem os ficheiros de configuração básica necessários para executar as suas funções.

## <a name="add-functions-to-the-app"></a>Adicionar funções para a aplicação

Os seguintes passos utilizam um modelo para criar o código de função durável no seu projeto.

1. Com o botão direito no projeto no Visual Studio e selecione **Add** > **nova função do Azure**.

    ![Adicionar nova função](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

2. Certifique-se **função do Azure** está selecionada no menu Adicionar e dê sua C# um nome de ficheiro.  Prima **Adicionar**.

3. Selecione o **orquestração de funções durável** modelo e clique em **Ok**

    ![Selecionar modelo durável](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

Uma nova função durável é adicionada à aplicação.  Abra o novo arquivo. CS para exibir o conteúdo. Esta função durável é uma função simples exemplo de encadeamento com os seguintes métodos:  

| Método | FunctionName | Descrição |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Gere a orquestração durável. Neste caso, a orquestração é iniciado, cria uma lista e adiciona o resultado de chamadas de três funções à lista.  Quando as chamadas de três função estiverem concluídas, devolve a lista. |
| **`SayHello`** | `<file-name>_Hello` | A função devolve um hello. Esta é a função que contém a lógica de negócios que está a ser orquestrada. |
| **`HttpStart`** | `<file-name>_HttpStart` | Uma [função acionada por HTTP](../functions-bindings-http-webhook.md) que inicia uma instância da orquestração e retorna uma resposta da verificação de estado. |

Agora que criou o projeto de função e uma função durável, pode testá-lo no seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

1. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função:

    ![Resposta da função localhost no browser](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A resposta é o resultado inicial da função HTTP nos informar a orquestração durável foi iniciado com êxito.  Ainda não é o resultado final da orquestração.  A resposta inclui alguns URLs útil.  Por enquanto, vamos consultar o estado da orquestração.

4. Copie o valor de URL para `statusQueryGetUri` e colá-la no endereço do browser da barra e execute o pedido.

    O pedido irá consultar a instância de orquestração para o estado. Obterá uma resposta eventual que é semelhante ao seguinte.  Isto mostra-na instância tiver sido concluída e inclui as saídas ou os resultados da função durável.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

5. Para parar a depuração, prima **Shift + F5**.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. Pode criar uma aplicação de funções diretamente a partir do Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base.

    O URL que chama o acionador de HTTP de função durável deve estar no seguinte formato:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Deve obter a mesma resposta como Estado, quando utilizar a aplicação publicada.

## <a name="next-steps"></a>Passos Seguintes

Utilizou o Visual Studio para criar e publicar um C# aplicação de funções durável.

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável.](durable-functions-concepts.md)
