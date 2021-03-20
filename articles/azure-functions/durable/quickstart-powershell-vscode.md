---
title: Crie a sua primeira função durável em Funções Azure utilizando o PowerShell
description: Crie e publique uma Função Azure Durável em PowerShell utilizando o Código de Estúdio Visual.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317264"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Crie a sua primeira função durável no PowerShell

*Funções Duradouras* é uma extensão de [Funções Azure](../functions-overview.md) que permite escrever funções imponentes num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

Neste artigo, aprende-se a usar a extensão das funções do Código do Estúdio Visual para criar localmente e testar uma função durável "hello world".  Esta função orquestrará e acorrentará as chamadas para outras funções. Em seguida, publique o código de função no Azure.

![Funcionamento durável em Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale [o Código do Estúdio Visual](https://code.visualstudio.com/download).

* Instale a extensão do Código [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS

* Certifique-se de que tem a versão mais recente das [Ferramentas Centrais Azure Functions](../functions-run-local.md).

* As funções duradouras requerem uma conta de armazenamento Azure. Precisa de uma assinatura Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local 

Nesta secção, você usa Código de Estúdio Visual para criar um projeto local de Funções Azure. 

1. No Código do Estúdio Visual, prima F1 (ou Ctrl/Cmd+Shift+P) para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Create New Project...` .

    ![Criar função](media/quickstart-js-vscode/functions-create-project.png)

1. Escolha uma localização de pasta vazia para o seu projeto e escolha **Select**.

1. Seguindo as indicações, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um idioma para o seu projeto de aplicação de função | PowerShell | Crie um projeto local de Funções PowerShell. |
    | Selecione uma versão | Funções Azure v3 | Só se vê esta opção quando as Ferramentas Core ainda não estão instaladas. Neste caso, as Ferramentas Core são instaladas na primeira vez que executam a aplicação. |
    | Selecione um modelo para a primeira função do seu projeto | Ignorar por agora | |
    | Selecione como gostaria de abrir o seu projeto | Abrir na janela atual | Reabre o Código VS na pasta selecionada. |

O Código do Estúdio Visual instala as Ferramentas Centrais de Funções Azure, se necessário. Também cria um projeto de aplicação de função numa pasta. Este projeto contém o [host.jse](../functions-host-json.md) [local.settings.jsem](../functions-run-local.md#local-settings-file) ficheiros de configuração.

Uma package.jsno ficheiro também é criada na pasta raiz.

### <a name="configure-function-app-to-use-powershell-7"></a>Configure a aplicação de função para usar o PowerShell 7

Abra a *local.settings.jsno* ficheiro e confirme que uma definição nomeada está definida `FUNCTIONS_WORKER_RUNTIME_VERSION` para `~7` . Se faltar ou definir para outro valor, atualize o conteúdo do ficheiro.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Crie as suas funções

A aplicação mais básica de Funções Duradouras contém três funções:

* *Função orquestrador* - descreve um fluxo de trabalho que orquestra outras funções.
* *Função de atividade* - chamada pela função orquestradora, executa o trabalho, e opcionalmente devolve um valor.
* *Função do cliente* - uma Função Azure regular que inicia uma função de orquestrador. Este exemplo utiliza uma função detonada HTTP.

### <a name="orchestrator-function"></a>Função orquestradora

Utilize um modelo para criar o código de função durável no seu projeto.

1. Na paleta de comando, procure e selecione `Azure Functions: Create Function...` .

1. Seguindo as indicações, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | Orquestrador de Funções Duráveis (pré-visualização) | Criar uma orquestração de funções duradouras |
    | Fornecer um nome de função | OláOrchestrator | Nome da sua função durável |

Adicionou um orquestrador para coordenar as funções de atividade. Abra *o HelloOrchestrator/run.ps1* para ver a função do orquestrador. Cada chamada para o `Invoke-ActivityFunction` cmdlet invoca uma função de atividade chamada `Hello` .

Em seguida, irá adicionar a função de `Hello` atividade referenciada.

### <a name="activity-function"></a>Função de atividade

1. Na paleta de comando, procure e selecione `Azure Functions: Create Function...` .

1. Seguindo as indicações, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | Atividade de funções duradouras (pré-visualização) | Criar uma função de atividade |
    | Fornecer um nome de função | Hello | Nome da sua função de atividade |

Acrescentou a `Hello` função de atividade que é invocada pelo orquestrador. Abra *a Hello/run.ps1* para ver se está tomando um nome como entrada e devolvendo uma saudação. Uma função de atividade é onde você executará ações como fazer uma chamada de base de dados ou realizar um cálculo.

Finalmente, irá adicionar uma função http desencadeada que inicia a orquestração.

### <a name="client-function-http-starter"></a>Função do cliente (entrada HTTP)

1. Na paleta de comando, procure e selecione `Azure Functions: Create Function...` .

1. Seguindo as indicações, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | Funções duradouras início HTTP (pré-visualização) | Criar uma função de arranque HTTP |
    | Fornecer um nome de função | HttpStart | Nome da sua função de atividade |
    | Nível de autorização | Anónimo | Para fins de demonstração, permita que a função seja chamada sem autenticação |

Adicionou uma função detonada HTTP que inicia uma orquestração. Abrir *httpStart/run.ps1* para ver se utiliza o `Start-NewOrchestration` cmdlet para iniciar uma nova orquestração. Em seguida, utiliza o `New-OrchestrationCheckStatusResponse` cmdlet para devolver uma resposta HTTP contendo URLs que podem ser usados para monitorizar e gerir a nova orquestração.

Tem agora uma aplicação Durable Functions que pode ser executada localmente e implantada no Azure.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. É solicitado que instale estas ferramentas na primeira vez que inicia uma aplicação de função a partir do Código do Estúdio Visual.

1. Para testar a sua função, desaponte um ponto de rutura no `Hello` código de função de atividade *(Olá/run.ps1).* Prima F5 ou selecione `Debug: Start Debugging` a partir da paleta de comando para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

    > [!NOTE]
    > Consulte os [Diagnósticos de Funções Duradouras](durable-functions-diagnostics.md#debugging) para obter mais informações sobre a depuração.

1. As funções duradouras requerem que uma conta de Armazenamento Azure funcione. Quando o Código VS lhe pedir para selecionar uma conta de armazenamento, escolha **selecione a conta de armazenamento**.

    ![Criar conta de armazenamento](media/quickstart-js-vscode/functions-select-storage.png)

1. Seguindo as indicações, forneça as seguintes informações para criar uma nova conta de armazenamento em Azure.

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecionar subscrição | *nome da sua assinatura* | selecione a subscrição do Azure |
    | Selecione uma conta de armazenamento | Criar uma nova conta de armazenamento |  |
    | Insira o nome da nova conta de armazenamento | *nome único* | Nome da conta de armazenamento para criar |
    | Selecionar um grupo de recursos | *nome único* | Nome do grupo de recursos para criar |
    | Selecionar uma localização | *região* | Selecione uma região próxima |

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](media/quickstart-js-vscode/functions-f5.png)

1. Utilizando o seu navegador, ou uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [o cURL,](https://curl.haxx.se/)envie um pedido HTTP POST para o ponto final do URL. Substitua o último segmento pelo nome da função orquestradora ( `HelloOrchestrator` ). O URL deve ser semelhante a `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   A resposta é o resultado inicial da função HTTP, informando-o de que a orquestração durável começou com sucesso. Ainda não é o resultado final da orquestração. A resposta inclui alguns URLs úteis. Por enquanto, vamos consultar o estado da orquestração.

1. Copie o valor URL `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute o pedido. Em alternativa, também pode continuar a usar o Carteiro para emitir o pedido GET.

   O pedido irá consultar a instância de orquestração para o estatuto. Você deve obter uma resposta eventual, o que nos mostra que o caso foi concluído, e inclui as saídas ou resultados da função durável. Parece que: 

    ```json
    {
        "name": "HelloOrchestrator",
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

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na sua subscrição Azure e, em seguida, implementa o seu código. 

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. 


1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    ![Publicar o projeto no Azure](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos pedidos:

    + **Selecione pasta**: Escolha uma pasta do seu espaço de trabalho ou navegue para uma que contenha a sua aplicação de função. Não verá isto se já tiver uma aplicação de função válida aberta.

    + **Selecione subscrição**: Escolha a subscrição para usar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione Aplicação de Funções no Azure**: Selecione `+ Create new Function App`. (Não escolha a `Advanced` opção, que não está abrangida por este artigo.)
      
    + **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome que seja válido num caminho URL. O nome que digita é validado para se certificar de que é único em Funções Azure. 

    + **Selecione um tempo de execução**: Escolha a versão do PowerShell que tem vindo a executar localmente. Pode utilizar o `pwsh -version` comando para verificar a sua versão.

        > [!NOTE]
        > A extensão do Código Azure Funções VS pode ainda não suportar o PowerShell 7. Se o PowerShell 7 não estiver disponível como opção, selecione PowerShell 6.x por enquanto e [atualize a versão manualmente](#update-function-app-ps7) após a criação da aplicação de função.

    + **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. 
    
1.  Quando concluídos, os seguintes recursos Azure são criados na sua subscrição, utilizando nomes baseados no nome da aplicação da sua função:
    
    + Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
    + Uma conta padrão de Armazenamento Azure, que mantém informações estatais e outras sobre os seus projetos.
    + Um plano de consumo, que define o anfitrião subjacente para a sua aplicação de função sem servidor. 
    + Uma aplicação de função, que fornece o ambiente para a execução do seu código de função. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação e partilha de recursos dentro do mesmo plano de hospedagem.
    + Uma instância de Insights de Aplicação ligada à aplicação de função, que rastreia o uso da sua função sem servidor.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado.

1. <a name="update-function-app-ps7"></a>Se não foi capaz de selecionar *PowerShell 7* mais cedo ao criar a aplicação de função, prima F1 (ou Ctrl/Cmd+Shift+P) para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Upload Local Settings...` . Siga as instruções para selecionar a aplicação de função que criou. Se for solicitado para substituir as definições existentes, selecione *Não a todas*.
    
1. Selecione **Ver Saída** nesta notificação para ver os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone da campainha no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. O URL que chama a sua função desencadeada por HTTP deve estar neste formato: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Deverá obter a mesma resposta de estado que antes quando utilizar a aplicação publicada.

## <a name="next-steps"></a>Passos seguintes

Utilizou o Código do Estúdio Visual para criar e publicar uma aplicação de função dura ção PowerShell.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)
