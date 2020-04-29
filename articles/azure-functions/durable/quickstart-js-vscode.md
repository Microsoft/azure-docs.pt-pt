---
title: Crie a sua primeira função durável em Azure usando javaScript
description: Crie e publique uma Função Durável Azure utilizando o Código do Estúdio Visual.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80257653"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Create your first durable function in JavaScript (Criar a sua primeira função durável em JavaScript)

*Funções Duráveis* é uma extensão das [Funções Azure](../functions-overview.md) que permite escrever funções imponentes num ambiente sem servidores. A extensão gere o estado, os pontos de verificação e os reinícios por si.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Neste artigo, aprende-se a usar a extensão visual do Código de Código Azure para criar e testar localmente uma função durável "hello world".  Esta função irá orquestrar e acorrentar chamadas em conjunto para outras funções. Em seguida, publique o código de função no Azure.

![Funcionamento da função durável em Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o Código do [Estúdio Visual](https://code.visualstudio.com/download).

* Instalar a extensão do código VS [funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

* Certifique-se de que tem a versão mais recente das [Ferramentas Core funções do Azure](../functions-run-local.md).

* Funções Duráveis requerem uma conta de armazenamento Azure. Precisa de uma assinatura Azure.

* Certifique-se de que tem a versão 10.x ou 12.x do [Node.js](https://nodejs.org/) instalada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local 

Nesta secção, você usa o Código de Estúdio Visual para criar um projeto local de Funções Azure. 

1. No Código do Estúdio Visual, prima F1 (ou CTRL/Cmd+Shift+P) para abrir a paleta de comando. Na paleta de comando, `Azure Functions: Create New Project...`procure e selecione .

    ![Criar função](media/quickstart-js-vscode/functions-create-project.png)

1. Escolha uma localização de pasta vazia para o seu projeto e escolha **Select**.

1. Seguindo as instruções, forneça as seguintes informações:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um idioma para o seu projeto de aplicação de funções | JavaScript | Crie um projeto local de Funções Node.js. |
    | Selecione uma versão | Funções Azure v3 | Só se vê esta opção quando as Ferramentas Core ainda não estão instaladas. Neste caso, as Ferramentas Core são instaladas na primeira vez que executa a aplicação. |
    | Selecione um modelo para a primeira função do seu projeto | Salta por enquanto | |
    | Selecione como pretende abrir o seu projeto | Abra na janela atual | Reabre o Código VS na pasta selecionada. |

O Visual Studio Code instala as Ferramentas Core funções Do Azure, se necessário. Também cria um projeto de aplicação de função numa pasta. Este projeto contém os ficheiros de configuração [host.json](../functions-host-json.md) e [local.settings.json.](../functions-run-local.md#local-settings-file)

Um ficheiro package.json também é criado na pasta raiz.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Ativar o modo de compatibilidade V2 funções do Azure

Atualmente, as funções duráveis JavaScript requerem que o modo de compatibilidade V2 das funções Azure seja ativado.

1. Abra *local.settings.json* para editar as definições utilizadas durante a execução da aplicação localmente.

1. Adicione uma `FUNCTIONS_V2_COMPATIBILITY_MODE` definição com `true`um valor de .

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureWebJobsStorage": "",
            "FUNCTIONS_WORKER_RUNTIME": "node",
            "FUNCTIONS_V2_COMPATIBILITY_MODE": "true"
        }
    }
    ```

## <a name="install-the-durable-functions-npm-package"></a>Instale o pacote NPM Funções Duráveis

Para trabalhar com Funções Duráveis numa aplicação de função `durable-functions`Node.js, utiliza-se uma biblioteca chamada .

1. Utilize o menu *'Ver'* ou Ctrl+Shift+' para abrir um novo terminal em Código VS.

1. Instale `durable-functions` o pacote `npm install durable-functions` npm executando o diretório raiz da aplicação de funções.

## <a name="creating-your-functions"></a>Criar as suas funções

A aplicação funções duráveis mais básica sintetiza três funções:

* *Função orchestrator* - descreve um fluxo de trabalho que orquestra outras funções.
* *Função de atividade* - chamada pela função orquestradora, executa o trabalho e opcionalmente devolve um valor.
* *Função do cliente* - uma Função Azure regular que inicia uma função de orquestrador. Este exemplo utiliza uma função ativada em HTTP.

### <a name="orchestrator-function"></a>Função orquestradora

Usa um modelo para criar o código de função durável no seu projeto.

1. Na paleta de comando, `Azure Functions: Create Function...`procure e selecione .

1. Seguindo as instruções, forneça as seguintes informações:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | Orquestrador de Funções Duráveis | Criar uma orquestração de Funções Duráveis |
    | Fornecer um nome de função | HelloOrchestrator | Nome da sua função durável |

Adicionou um orquestrador para coordenar as funções de atividade. Abra *HelloOrchestrator/index.js* para ver a função de orquestrador. Cada chamada `context.df.callActivity` para invocar uma `Hello`função de atividade chamada .

Em seguida, irá adicionar `Hello` a função de atividade referenciada.

### <a name="activity-function"></a>Função de atividade

1. Na paleta de comando, `Azure Functions: Create Function...`procure e selecione .

1. Seguindo as instruções, forneça as seguintes informações:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | Atividade de Funções Duráveis | Criar uma função de atividade |
    | Fornecer um nome de função | Hello | Nome da sua função de atividade |

Adicionou a `Hello` função de atividade que é invocada pelo orquestrador. Abra *hello/index.js* para ver que está tomando um nome como entrada e devolvendo uma saudação. Uma função de atividade é onde você irá realizar ações como fazer uma chamada de base de dados ou realizar um cálculo.

Finalmente, você adicionará uma função http desencadeada que inicia a orquestração.

### <a name="client-function-http-starter"></a>Função cliente (início HTTP)

1. Na paleta de comando, `Azure Functions: Create Function...`procure e selecione .

1. Seguindo as instruções, forneça as seguintes informações:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a sua função | Funções Duráveis HTTP starter | Criar uma função de arranque HTTP |
    | Fornecer um nome de função | Funções DuráveisHttpStart | Nome da sua função de atividade |
    | Nível de autorização | Anónimo | Para fins de demonstração, permita que a função seja chamada sem autenticação |

Adicionou uma função ativada em HTTP que inicia uma orquestração. Open *DurableFunctionsHttpStart/index.js* para ver `client.startNew` se usa para iniciar uma nova orquestração. Em seguida, usa `client.createCheckStatusResponse` para devolver uma resposta HTTP contendo URLs que podem ser usados para monitorizar e gerir a nova orquestração.

Tem agora uma aplicação De Funções Duráveis que pode ser executada localmente e implantada para o Azure.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deve instalar estas ferramentas ao iniciar uma função do Visual Studio Code pela primeira vez.

1. Para testar a sua função, `Hello` delineie um ponto de rutura no código de função de atividade *(Olá/index.js*). Prima F5 `Debug: Start Debugging` ou selecione a partir da paleta de comando para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

    > [!NOTE]
    > Consulte os Diagnósticos de [Funções Duráveis](durable-functions-diagnostics.md#debugging) para obter mais informações sobre depuração.

1. Funções Duráveis requer uma conta de Armazenamento Azure para funcionar. Quando o Código VS lhe pedir para selecionar uma conta de armazenamento, escolha **a conta de armazenamento Select**.

    ![Criar conta de armazenamento](media/quickstart-js-vscode/functions-select-storage.png)

1. Seguindo as instruções, forneça as seguintes informações para criar uma nova conta de armazenamento no Azure.

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecionar subscrição | *nome da sua assinatura* | selecione a subscrição do Azure |
    | Selecione uma conta de armazenamento | Criar uma nova conta de armazenamento |  |
    | Insira o nome da nova conta de armazenamento | *nome único* | Nome da conta de armazenamento para criar |
    | Selecionar um grupo de recursos | *nome único* | Nome do grupo de recursos para criar |
    | Selecionar uma localização | *região* | Selecione uma região próxima de si |

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](media/quickstart-js-vscode/functions-f5.png)

1. Utilizando uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [cURL,](https://curl.haxx.se/)envie um pedido HTTP POST para o ponto final do URL. Substitua o último segmento com o`HelloOrchestrator`nome da função orquestradora ( ). O URL deve `http://localhost:7071/api/orchestrators/HelloOrchestrator`ser semelhante a .

   A resposta é o resultado inicial da função HTTP, permitindo-lhe saber que a orquestração durável começou com sucesso. Ainda não é o resultado final da orquestração. A resposta inclui alguns URLs úteis. Por enquanto, vamos consultar o estado da orquestração.

1. Copie o `statusQueryGetUri` valor de URL e cole-o na barra de endereços do navegador e execute o pedido. Em alternativa, também pode continuar a utilizar o Carteiro para emitir o pedido GET.

   O pedido irá consultar a instância de orquestração para o estatuto. Deve obter uma resposta eventual, o que nos mostra que a instância já terminou, e inclui as saídas ou resultados da função durável. Parece que: 

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

1. Para parar de depurar, prima **Shift + F5** no Código VS.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Ativar o modo de compatibilidade V2 funções do Azure

A mesma compatibilidade azure Funções V2 que permitiu localmente precisa ser ativada na aplicação em Azure.

1. Utilizando a paleta de comando, procure e selecione `Azure Functions: Edit Setting...`.

1. Siga as instruções para localizar a sua aplicação de função na subscrição do Azure.

1. Selecione `Create new App Setting...`.

1. Introduza uma nova `FUNCTIONS_V2_COMPATIBILITY_MODE`tecla de definição de .

1. Introduza um `true`valor de definição de .

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. O URL que chama a função de HTTP deve estar neste formato:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Deve obter a mesma resposta de estado que antes ao utilizar a aplicação publicada.

## <a name="next-steps"></a>Passos seguintes

Usou o Código do Estúdio Visual para criar e publicar uma aplicação de função durável JavaScript.

> [!div class="nextstepaction"]
> [Conheça os padrões comuns de função durável](durable-functions-overview.md#application-patterns)