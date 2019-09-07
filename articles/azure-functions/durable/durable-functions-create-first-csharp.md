---
title: Criar sua primeira função durável no Azure usandoC#
description: Criar e publicar uma função durável do Azure usando o Visual Studio.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: azfuncdf
ms.openlocfilehash: e34d000f77ca5a5ac75fadab61b4280d528c6805
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735296"
---
# <a name="create-your-first-durable-function-in-c"></a>Criar sua primeira função durável em C\#

*Durable Functions* é uma extensão de [Azure Functions](../functions-overview.md) que permite que você escreva funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

Neste artigo, você aprenderá a usar o Visual Studio 2019 para criar e testar localmente uma função durável "Olá, mundo".  Essa função orquestra e encadeia chamadas para outras funções. Em seguida, publique o código de função no Azure. Essas ferramentas estão disponíveis como parte da carga de trabalho de desenvolvimento do Azure no Visual Studio 2019.

![Executando a função durável no Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Verifique se a carga de trabalho de **desenvolvimento do Azure** também está instalada. O Visual Studio 2017 também dá suporte ao desenvolvimento de Durable Functions, mas a interface do usuário e as etapas são diferentes.

* Verifique se você tem o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) instalado e em execução.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

O modelo de Azure Functions cria um projeto que pode ser publicado em um aplicativo de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. No Visual Studio, selecione **Novo** > **Projeto**, no menu **Ficheiro**.

1. Na caixa de diálogo **Adicionar um novo projeto** , procure `functions`, escolha o modelo de **Azure Functions** e selecione **Avançar**. 

    ![Caixa de diálogo Novo projeto para criar uma função no Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Digite um **nome de projeto** para seu projeto e selecione **OK**. O nome do projeto deve ser válido como C# um namespace, portanto, não use sublinhados, hifens ou quaisquer outros caracteres não alfanuméricos.

1. Em **criar um novo aplicativo Azure Functions**, use as configurações especificadas na tabela que segue a imagem.

    ![Criar uma nova caixa de diálogo Azure Functions aplicativo no Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Definição      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Funções do Azure 2.x <br />(.NET Core) | Cria um projeto de função que usa o tempo de execução da versão 2. x de Azure Functions, que dá suporte ao .NET Core. As Funções do Azure 1.x suportam o .NET Framework. Para obter mais informações, veja [How to target Azure Functions runtime version](../functions-versions.md) (Como segmentar a versão do runtime das Funções do Azure).   |
    | **Modelo** | Vazio | Cria um aplicativo de funções vazio. |
    | **Conta de armazenamento**  | Emulador do Armazenamento | Uma conta de armazenamento é necessária para o gerenciamento de estado de função durável. |

4. Selecione **criar** para criar um projeto de função vazio. Este projeto tem os arquivos de configuração básicos necessários para executar suas funções.

## <a name="add-functions-to-the-app"></a>Adicionar funções ao aplicativo

As etapas a seguir usam um modelo para criar o código de função durável em seu projeto.

1. Clique com o botão direito do mouse no projeto no Visual Studio e selecione **Adicionar** > **nova função do Azure**.

    ![Adicionar nova função](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Verifique se a **função do Azure** está selecionada no menu Adicionar, digite um nome C# para o arquivo e, em seguida, selecione **Adicionar**.

1. Selecione o modelo de **orquestração Durable Functions** e, em seguida, selecione **OK**

    ![Selecionar modelo durável](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

Uma nova função durável é adicionada ao aplicativo.  Abra o novo arquivo. cs para exibir o conteúdo. Essa função durável é um exemplo simples de encadeamento de funções com os seguintes métodos:  

| Método | FunctionName | Descrição |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Gerencia a orquestração durável. Nesse caso, a orquestração inicia, cria uma lista e adiciona o resultado de três chamadas de funções à lista.  Quando as três chamadas de função são concluídas, ela retorna a lista. |
| **`SayHello`** | `<file-name>_Hello` | A função retorna uma saudação. Essa é a função que contém a lógica de negócios que está sendo orquestrada. |
| **`HttpStart`** | `<file-name>_HttpStart` | Uma [função disparada por http](../functions-bindings-http-webhook.md) que inicia uma instância da orquestração e retorna uma resposta de verificação de status. |

Agora que você criou seu projeto de função e uma função durável, você pode testá-lo em seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

1. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Cole a URL da solicitação HTTP na barra de endereços do navegador e execute a solicitação. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função:

    ![Resposta da função localhost no browser](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A resposta é o resultado inicial da função HTTP que nos permite saber que a orquestração durável foi iniciada com êxito.  Ainda não é o resultado final da orquestração.  A resposta inclui algumas URLs úteis.  Por enquanto, vamos consultar o status da orquestração.

4. Copie o valor da URL `statusQueryGetUri` para e cole-o na barra de endereços do navegador e execute a solicitação.

    A solicitação consultará a instância de orquestração do status. Você deve obter uma resposta eventual semelhante à seguinte.  Isso nos mostra que a instância foi concluída e inclui as saídas ou os resultados da função durável.

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

    A URL que chama o gatilho HTTP da função durável deve estar no seguinte formato:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Você deve obter a mesma resposta de status que antes de usar o aplicativo publicado.

## <a name="next-steps"></a>Passos seguintes

Você usou o Visual Studio para criar e publicar um C# aplicativo de funções duráveis.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável.](durable-functions-concepts.md)