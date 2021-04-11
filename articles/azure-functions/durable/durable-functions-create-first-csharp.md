---
title: 'Crie a sua primeira função durável em Azure utilizando C #'
description: Crie e publique uma Função Azure Durável utilizando o Visual Studio ou o Visual Studio Code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: 36566baac2885c449e1eae762924357bf571b39f
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490221"
---
# <a name="create-your-first-durable-function-in-c"></a>Crie a sua primeira função durável em C\#

*Funções Duradouras* é uma extensão de [Funções Azure](../functions-overview.md) que permite escrever funções imponentes num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

::: zone pivot="code-editor-vscode"

Neste artigo, você aprende a usar o Código do Estúdio Visual para criar e testar uma função durável "hello world".  Esta função orquestra e acorrenta chamadas para outras funções. Em seguida, publique o código de função no Azure. Estas ferramentas estão disponíveis como parte da extensão vs Code [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![A screenshot mostra uma janela visual Studio Code com uma função durável.](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale [o Código do Estúdio Visual](https://code.visualstudio.com/download).

* Instale as seguintes extensões do Código VS:
    - [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Certifique-se de que tem a versão mais recente das [Ferramentas Centrais Azure Functions](../functions-run-local.md).

* As funções duradouras requerem uma conta de armazenamento Azure. Precisa de uma assinatura Azure.

* Certifique-se de que tem a versão 3.1 ou uma versão posterior do [.NET Core SDK](https://dotnet.microsoft.com/download) instalado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local 

Nesta secção, você usa Código de Estúdio Visual para criar um projeto local de Funções Azure. 

1. No Código do Estúdio Visual, prima F1 (ou Ctrl/Cmd+Shift+P) para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Create New Project...` .

    ![Criar um projeto de função](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Escolha uma localização de pasta vazia para o seu projeto e escolha **Select**.

1. Seguindo as indicações, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um idioma para o seu projeto de aplicação de função | C# | Crie um projeto local de funções C#. |
    | Selecione uma versão | Funções Azure v3 | Só se vê esta opção quando as Ferramentas Core ainda não estão instaladas. Neste caso, as Ferramentas Core são instaladas na primeira vez que executam a aplicação. |
    | Selecione um modelo para a primeira função do seu projeto | Ignorar por agora | |
    | Selecione como gostaria de abrir o seu projeto | Abrir na janela atual | Reabre o Código VS na pasta selecionada. |

O Código do Estúdio Visual instala as Ferramentas Centrais de Funções Azure, se necessário. Também cria um projeto de aplicação de função numa pasta. Este projeto contém o [host.jse](../functions-host-json.md) [local.settings.jsem](../functions-run-local.md#local-settings-file) ficheiros de configuração.

## <a name="add-functions-to-the-app"></a>Adicionar funções à aplicação

Os passos seguintes utilizam um modelo para criar o código de função durável no seu projeto.

1. Na paleta de comando, procure e selecione `Azure Functions: Create Function...` .

1. Seguindo as indicações, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | DurávelFunctionsOrchestration | Criar uma orquestração de funções duradouras |
    | Fornecer um nome de função | OláOrchestração | Nome da classe em que as funções são criadas |
    | Fornecer um espaço de nome | Empresa.Função | Espaço de nome para a classe gerada |

1. Quando o Código VS lhe pedir para selecionar uma conta de armazenamento, escolha **selecione a conta de armazenamento**. Seguindo as indicações, forneça as seguintes informações para criar uma nova conta de armazenamento em Azure.

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecionar subscrição | *nome da sua assinatura* | selecione a subscrição do Azure |
    | Selecione uma conta de armazenamento | Criar uma nova conta de armazenamento |  |
    | Insira o nome da nova conta de armazenamento | *nome único* | Nome da conta de armazenamento para criar |
    | Selecionar um grupo de recursos | *nome único* | Nome do grupo de recursos para criar |
    | Selecionar uma localização | *region* | Selecione uma região próxima |

Uma classe que contenha as novas funções é adicionada ao projeto. O Código VS também adiciona o fio de ligação da conta de armazenamento a *local.settings.js* e uma referência ao [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) pacote NuGet ao ficheiro do projeto *.csproj.*

Abra o novo ficheiro *HelloOrchestration.cs* para visualizar o conteúdo. Esta função durável é um exemplo simples de acorrentação de funções com os seguintes métodos:  

| Método | Nome de função | Descrição |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Gere a orquestração duradoura. Neste caso, a orquestração começa, cria uma lista, e adiciona o resultado de três funções chamadas à lista.  Quando as três chamadas de função estiverem completas, retorna a lista. |
| **`SayHello`** | `HelloOrchestration_Hello` | A função devolve um olá. É a função que contém a lógica de negócio que está a ser orquestrada. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | Uma [função desencadeada por HTTP](../functions-bindings-http-webhook.md) que inicia uma instância da orquestração e devolve uma resposta ao estado de verificação. |

Agora que criou o seu projeto de função e uma função durável, pode testá-lo no seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deve instalar estas ferramentas ao iniciar uma função do Visual Studio Code pela primeira vez.

1. Para testar a sua função, desaponte um ponto de rutura no código de função de `SayHello` atividade e prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

    > [!NOTE]
    > Consulte os [Diagnósticos de Funções Duradouras](durable-functions-diagnostics.md#debugging) para obter mais informações sobre a depuração.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Utilizando uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [o CURL,](https://curl.haxx.se/)envie um pedido HTTP POST para o ponto final do URL.

   A resposta é o resultado inicial da função HTTP, informando-nos que a orquestração durável começou com sucesso. Ainda não é o resultado final da orquestração. A resposta inclui alguns URLs úteis. Por enquanto, vamos consultar o estado da orquestração.

1. Copie o valor URL `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute o pedido. Em alternativa, também pode continuar a usar o Carteiro para emitir o pedido GET.

   O pedido irá consultar a instância de orquestração para o estatuto. Você deve obter uma resposta eventual, o que nos mostra que o caso foi concluído, e inclui as saídas ou resultados da função durável. Parece que: 

    ```json
    {
        "name": "HelloOrchestration",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Para parar de depurar, prima **Shift + F5** em Código VS.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

    `https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart`

1. Cole este novo URL do pedido HTTP na barra de endereço do browser. Deverá obter a mesma resposta de estado que antes quando utilizar a aplicação publicada.

## <a name="next-steps"></a>Passos seguintes

Utilizou o Código do Estúdio Visual para criar e publicar uma aplicação de função duradoura C#.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

Neste artigo, aprende-se a usar o Visual Studio 2019 para criar e testar localmente uma função durável "Hello world".  Esta função orquestra e acorrenta chamadas para outras funções. Em seguida, publique o código de função no Azure. Estas ferramentas estão disponíveis como parte da carga de trabalho de desenvolvimento do Azure no Visual Studio 2019.

![A screenshot mostra uma janela visual Studio 2019 com uma função durável.](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instalar [o Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Certifique-se de que a carga de trabalho **de desenvolvimento do Azure** também está instalada. O Visual Studio 2017 também suporta o desenvolvimento de Funções Duráveis, mas a UI e os passos diferem.

* Verifique se tem o [Emulador de Armazenamento Azure](../../storage/common/storage-use-emulator.md) instalado e em funcionamento.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

O modelo Azure Functions cria um projeto que pode ser publicado numa aplicação de função em Azure. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação, escala e partilha de recursos.

1. No Visual Studio, selecione **New** > **Project** (Novo Projeto) no menu **File** (Ficheiro).

1. No **Criar um novo** diálogo de projeto, `functions` procure, escolha o modelo **Azure Functions** e selecione **Seguinte**. 

    ![Caixa de diálogo Novo projeto para criar uma função no Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Digite um **nome de projeto** para o seu projeto e selecione **OK**. O nome do projeto deve ser válido como um espaço de nome C#, por isso não use sublinhados, hífens ou quaisquer outros caracteres não antanhanuméricos.

1. Na **Criação de uma nova Aplicação de Funções Azure,** utilize as definições especificadas na tabela que segue a imagem.

    ![Criar um novo diálogo de aplicação Azure Functions em Estúdio Visual](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Definição      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Funções Azure 3.0 <br />(.NET Core) | Cria um projeto de função que utiliza a versão 3.0 do tempo de execução das Funções Azure, que suporta .NET Core 3.1. Para obter mais informações, veja [How to target Azure Functions runtime version](../functions-versions.md) (Como segmentar a versão do runtime das Funções do Azure).   |
    | **Modelo** | Vazio | Cria uma aplicação de função vazia. |
    | **Conta de armazenamento**  | Emulador do Armazenamento | É necessária uma conta de armazenamento para uma gestão estatal de funções duradouras. |

4. Selecione **Criar** para criar um projeto de função vazio. Este projeto tem os ficheiros de configuração básicos necessários para executar as suas funções.

## <a name="add-functions-to-the-app"></a>Adicionar funções à aplicação

Os passos seguintes utilizam um modelo para criar o código de função durável no seu projeto.

1. Clique com o botão direito no projeto no Estúdio Visual e **selecione Add**  >  **New Azure Function**.

    ![Adicionar nova função](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Verifique se **a Função Azure** está selecionada a partir do menu de adicionar, digite um nome para o seu ficheiro C# e, em seguida, selecione **Adicionar**.

1. Selecione o modelo **de orquestração funções duradouras** e, em seguida, selecione **Ok**

    ![Selecione modelo durável](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Uma nova função durável é adicionada à aplicação.  Abra o novo ficheiro .cs para ver o conteúdo. Esta função durável é um exemplo simples de acorrentação de funções com os seguintes métodos:  

| Método | Nome de função | Descrição |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Gere a orquestração duradoura. Neste caso, a orquestração começa, cria uma lista, e adiciona o resultado de três funções chamadas à lista.  Quando as três chamadas de função estiverem completas, retorna a lista. |
| **`SayHello`** | `<file-name>_Hello` | A função devolve um olá. É a função que contém a lógica de negócio que está a ser orquestrada. |
| **`HttpStart`** | `<file-name>_HttpStart` | Uma [função desencadeada por HTTP](../functions-bindings-http-webhook.md) que inicia uma instância da orquestração e devolve uma resposta ao estado de verificação. |

Agora que criou o seu projeto de função e uma função durável, pode testá-lo no seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

1. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Cole o URL para o pedido HTTP na barra de endereços do seu navegador e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função:

    ![A screenshot mostra uma janela do navegador com o statusQueryGetUri chamado.](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A resposta é o resultado inicial da função HTTP, informando-nos que a orquestração durável começou com sucesso.  Ainda não é o resultado final da orquestração.  A resposta inclui alguns URLs úteis.  Por enquanto, vamos consultar o estado da orquestração.

4. Copie o valor URL `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute o pedido.

    O pedido irá consultar a instância de orquestração para o estatuto. Devias ter uma resposta eventual que se pareça com o seguinte.  Esta saída mostra-nos que a instância foi concluída, e inclui as saídas ou resultados da função durável.

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
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Para parar a depuração, prima **Shift + F5**.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. Pode criar uma aplicação de funções diretamente a partir do Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base.

    O URL que chama a sua função duradoura HTTP trigger deve estar no seguinte formato:

    `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart`

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Deverá obter a mesma resposta de estado que antes quando utilizar a aplicação publicada.

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio para criar e publicar uma aplicação de função duradoura C#.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)

::: zone-end
