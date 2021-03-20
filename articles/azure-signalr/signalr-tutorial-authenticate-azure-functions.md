---
title: 'Tutorial: Autenticação com Funções Azure - Azure SignalR'
description: Neste tutorial, aprende a autenticar clientes do Serviço Azure SignalR para a ligação de funções Azure
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 6df47d3fd62083a5d0940a1d6da50ac5d7d955f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150915"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Tutorial: autenticação do Azure SignalR Service com as Funções do Azure Functions

Tutorial passo a passo para criar uma sala de chat com autenticação e mensagens privadas com as Funções do Azure, Autenticação do Serviço de Aplicações e SignalR Service.

## <a name="introduction"></a>Introdução

### <a name="technologies-used"></a>Tecnologias utilizadas

* [Funções do Azure](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): API de back-end para autenticar utilizadores e enviar mensagens de chat
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): permite difundir novas mensagens para clientes de chat ligados
* [Armazenamento do Azure](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): permite alojar o site estático para a IU do cliente de chat

### <a name="prerequisites"></a>Pré-requisitos

É necessário o software seguinte para criar este tutorial.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (versão 10.x)
* [SDK .NET](https://www.microsoft.com/net/download) (versão 2.x, necessário para extensões das Funções)
* [Ferramentas de Núcleo de Funções do Azure](https://github.com/Azure/azure-functions-core-tools) (versão 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) com as seguintes extensões
  * [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions): trabalhe com as Funções do Azure no VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer): permite fornecer páginas Web localmente para teste

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Aceda ao [portal do Azure](https://portal.azure.com/) e inicie sessão com as suas credenciais.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="create-an-azure-signalr-service-instance"></a>Criar uma instância do Azure SignalR Service

Vai criar e testar a aplicação Funções do Azure localmente. A aplicação acede a uma instância do SignalR Service no Azure que tem de ser criada antecipadamente.

1. Clique no botão **Criar um** **+** recurso () para criar um novo recurso Azure.

1. Procure **SignalR Service** e selecione-o. Clique em **Criar**.

    ![Novo SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Introduza as seguintes informações.

    | Name | Valor |
    |---|---|
    | Nome do recurso | Nome exclusivo da instância do SignalR Service |
    | Grupo de recursos | Criar um novo grupo de recursos com um nome único |
    | Localização | Selecione uma localização perto de si |
    | Escalão de Preço | Gratuito |

1. Clique em **Criar**.

1. Depois de implementada a instância, abra-a no portal e localize a sua página de Definições. Altere a definição do modo de serviço para *sem servidor*.

    ![Modo de serviço SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="initialize-the-function-app"></a>Inicializar a aplicação de funções

### <a name="create-a-new-azure-functions-project"></a>Criar um novo projeto das Funções do Azure

1. Numa nova janela do VS Code, utilize `File > Open Folder` no menu para criar e abrir uma pasta vazia numa localização adequada. Será a pasta de projetos principal da aplicação que vai criar.

1. Através da extensão das Funções do Azure no VS Code, inicialize uma aplicação de Funções na pasta de projetos principal.
   1. Abra a Paleta de Comandos no VS Code ao selecionar **View > Command Palette** (Ver > Paleta de Comandos) no menu (atalho `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
   1. Procure o comando **Azure Functions: Create New Project** (Funções do Azure: Criar Novo Projeto) e selecione-o.
   1. Deverá aparecer a pasta de projetos principal. Selecione-a (ou utilize a opção "Procurar" para localizá-la).
   1. Na linha de comandos para escolher um idioma, selecione **JavaScript**.

      ![Criar uma aplicação de funções](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Instalar as extensões da aplicação de funções

Este tutorial utiliza enlaces das Funções do Azure para interagir com o Azure SignalR Service. Como a maior parte dos outros enlaces, os enlaces do SignalR Service estão disponíveis como uma extensão que tem de ser instalada através da CLI das Ferramentas de Núcleo de Funções do Azure antes de poderem ser utilizados.

1. Abra um terminal no Código VS selecionando **Ver > Terminal** a partir do menu (Ctrl- \` ).

1. Certifique-se de que a pasta de projetos principal é o diretório atual.

1. Instale a extensão da aplicação de funções do SignalR Service.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>Configurar definições da aplicação

Quando executar e depurar o runtime das Funções do Azure localmente, as definições da aplicação são lidas a partir de **local.settings.json**. Atualize este ficheiro com a cadeia de ligação da instância do SignalR Service que criou anteriormente.

1. No VS Code, selecione **local.settings.json** no painel Explorer para abri-lo.

1. Substitua o conteúdo do ficheiro pelo seguinte.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Introduza a cadeia de ligação do Azure SignalR Service para uma definição chamada `AzureSignalRConnectionString`. Obtenha o valor a partir da página **Keys** (Chaves) no recurso do Azure SignalR Service no portal do Azure; pode ser utilizada a cadeia de ligação primária ou secundária.
   * A definição `WEBSITE_NODE_DEFAULT_VERSION` não é utilizada localmente, mas é necessária quando for implementada no Azure.
   * A secção `Host` configura as definições de porta e CORS do anfitrião local das Funções (esta definição não tem efeito quando estiver em execução no Azure).

       > [!NOTE]
       > O Live Server é tipicamente configurado para servir conteúdo a partir de `http://127.0.0.1:5500` . Se descobrir que está a utilizar um URL diferente ou se estiver a utilizar um servidor HTTP diferente, altere a `CORS` definição para refletir a origem correta.

     ![Obter a chave do SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Guarde o ficheiro.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Criar uma função para autenticar os utilizadores no SignalR Service

Quando a aplicação de chat é aberta pela primeira vez no browser, requer credenciais de ligação válidas para ligar ao Azure SignalR Service. Criará uma função httpizada chamada *negociar* na sua app de função para devolver esta informação de ligação.

> [!NOTE]
> Esta função deve ser nomeada *negociar,* uma vez que o cliente SignalR requer um ponto final que termina em `/negotiate` .

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Procure e selecione o comando **Azure Functions: Create Function** (Funções do Azure: Criar Função).

1. Quando lhe for pedido, forneça as seguintes informações.

    | Nome | Valor |
    |---|---|
    | Pasta da aplicação de funções | Selecione a pasta de projetos principal |
    | Modelo | Acionador HTTP |
    | Nome | negotiate |
    | Nível de autorização | Anónimo |

    É criada uma pasta chamada **negotiate** que contém a nova função.

1. Abrir **negociação/function.jspara** configurar encadernações para a função. Modifique o conteúdo do ficheiro para o seguinte. Esta ação adiciona um enlace de entrada que gera credenciais válidas para um cliente ligar a um hub do Azure SignalR Service chamado `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    A propriedade `userId` no enlace `signalRConnectionInfo` é utilizada para criar uma ligação autenticada ao SignalR Service. Deixe a propriedade em branco para desenvolvimento local. Vai utilizá-la quando a aplicação de funções for implementada no Azure.

1. Abrir **negociar/index.js** para ver o corpo da função. Modifique o conteúdo do ficheiro para o seguinte.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Esta função recebe as informações de ligação do SignalR a partir do enlace de entrada e devolve-as ao cliente no corpo da resposta HTTP. O cliente SignalR utilizará esta informação para se ligar à instância do Serviço SignalR.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-send-chat-messages"></a>Criar uma função para enviar mensagens de chat

A aplicação Web também requer uma API HTTP para enviar mensagens de chat. Vai criar uma função acionada por HTTP chamada *SendMessage* que envia mensagens a todos os clientes ligados através do SignalR Service.

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Procure e selecione o comando **Azure Functions: Create Function** (Funções do Azure: Criar Função).

1. Quando lhe for pedido, forneça as seguintes informações.

    | Nome | Valor |
    |---|---|
    | Pasta da aplicação de funções | Selecione a pasta de projetos principal |
    | Modelo | Acionador HTTP |
    | Nome | SendMessage |
    | Nível de autorização | Anónimo |

    É criada uma pasta chamada **SendMessage** com a nova função.

1. Abra **SendMessage/function.json** para configurar os enlaces da função. Modifique o conteúdo do ficheiro para o seguinte.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Isto faz duas alterações à função original:
    * Altera o caminho para `messages` e restringe o acionador HTTP ao método HTTP **POST**.
    * Adiciona uma ligação de saída do Serviço SignalR que envia uma mensagem devolvida pela função a todos os clientes ligados a um hub de Serviço SignalR chamado `chat` .

1. Guarde o ficheiro.

1. Abra **SendMessage/index.js** para ver o corpo da função. Modifique o conteúdo do ficheiro para o seguinte.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Esta função utiliza o corpo do pedido HTTP e envia-o aos clientes ligados ao SignalR Service e invoca uma função chamada `newMessage` em cada cliente.

    A função pode ler a identidade do remetente e aceitar um valor *recipient* no corpo da mensagem para permitir o envio de uma mensagem em privado para um único utilizador. Estas funcionalidades serão utilizadas mais tarde no tutorial.

1. Guarde o ficheiro.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Criar e executar a interface de utilizador Web do cliente de chat

A IU da aplicação de chat é uma aplicação de página única simples (SPA) criada com a arquitetura Vue JavaScript. Será alojada separada da aplicação de funções. Localmente, vai executar a interface Web com a extensão Live Server VS Code.

1. No VS Code, crie uma nova pasta chamada **content** na raiz da pasta de projetos principal.

1. Na pasta **content**, crie um novo ficheiro chamado **index.html**.

1. Copie e cole o conteúdo de **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)**.

1. Guarde o ficheiro.

1. Prima **F5** para executar a aplicação de funções localmente e anexar um depurador.

1. Com **index.html** aberto, inicie o Live Server ao abrir a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) e selecione **Live Server: Open with Live Server** (Live Server: Abrir com o Live Server). O Live Server irá abrir a aplicação num browser.

1. É aberta a aplicação. Introduza uma mensagem na caixa de chat e prima Enter. Atualize a aplicação para ver novas mensagens. Uma vez que não foi configurada nenhuma autenticação, todas as mensagens serão enviadas como "anónimas".

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="deploy-to-azure-and-enable-authentication"></a>Implementar no Azure e ativar a autenticação

Tem estado a executar a aplicação de funções e a aplicação de chat localmente. Vai agora implementá-las no Azure e ativar a autenticação e as mensagens privadas na aplicação.

### <a name="log-into-azure-with-vs-code"></a>Iniciar sessão no Azure com o VS Code

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Procure e selecione o comando **Azure: Sign in** (Azure: iniciar sessão).

1. Siga as instruções para concluir o processo de início de sessão no browser.

### <a name="create-a-storage-account"></a>Criar uma conta de Armazenamento

Uma conta de Armazenamento Azure é exigida por uma aplicação de função em execução em Azure. Também irá hospedar a página web para o chat UI utilizando a funcionalidade de sites estáticos do Azure Storage.

1. No portal Azure, clique no botão **Criar um recurso** ( ) para criar um novo recurso **+** Azure.

1. Selecione a categoria **Armazenamento** e, em seguida, selecione **a conta de Armazenamento**.

1. Introduza as seguintes informações.

    | Name | Valor |
    |---|---|
    | Subscrição | Selecione a subscrição que contém a instância do Serviço SignalR |
    | Grupo de recursos | Selecione o mesmo grupo de recursos |
    | Nome do recurso | Um nome único para a conta de Armazenamento |
    | Localização | Selecione o mesmo local que os seus outros recursos |
    | Desempenho | Standard |
    | Tipo de conta | StorageV2 (fins gerais v2) |
    | Replicação | Armazenamento localmente redundante (LRS) |
    | Nível de acesso | Frequente |

1. Clique **em Rever + criar** e, em seguida, **Criar**.

### <a name="configure-static-websites"></a>Configurar sites estáticos

1. Depois da conta de Armazenamento ser criada, abra-a no portal Azure.

1. Selecione **site estático**.

1. Selecione **Ativado** para ativar a funcionalidade do site estático.

1. No **nome do documento índice**, insiraindex.htm *l*.

1. Clique em **Guardar**.

1. Aparece um **ponto final primário.** Reparem neste valor. Será necessário configurar a aplicação de função.

### <a name="configure-function-app-for-authentication"></a>Configurar a aplicação de funções para autenticação

Até agora, a aplicação de chat funciona anonimamente. No Azure, utilizará a [Autenticação do Serviço de Aplicações](../app-service/overview-authentication-authorization.md) para autenticar o utilizador. O ID ou nome de utilizador autenticado pode ser transmitido ao enlace *SignalRConnectionInfo* para gerar informações de ligação que são autenticadas como o utilizador.

Ao enviar uma mensagem, a aplicação pode decidir se a envia para todos os clientes ligados ou apenas para os clientes que tenham sido autenticados para um determinado utilizador.

1. No Código VS, abrir **negociação/function.jsem**.

1. Insira uma [expressão de enlace](../azure-functions/functions-triggers-bindings.md) na propriedade *userId* do enlace *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. É definido o valor para o nome do utilizador autenticado. O atributo deverá ter agora o seguinte aspeto.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Guarde o ficheiro.


### <a name="deploy-function-app-to-azure"></a>Implementar app de função para Azure

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) e selecione **Azure Functions: Deploy to Function App** (Funções do Azure: Implementar uma Aplicação de Funções).

1. Quando lhe for pedido, forneça as seguintes informações.

    | Nome | Valor |
    |---|---|
    | Pasta para implementar | Selecione a pasta de projetos principal |
    | Subscrição | Selecione a sua subscrição |
    | Aplicação de funções | Selecione **Criar Nova Aplicação de Funções** |
    | Nome da aplicação de funções | Introduza um nome exclusivo |
    | Grupo de recursos | Selecione o mesmo grupo de recursos da instância do SignalR Service |
    | Conta de armazenamento | Selecione a conta de armazenamento que criou anteriormente |

    É criada uma nova aplicação de funções no Azure e a implementação é iniciada. Aguarde pela conclusão da implementação.

### <a name="upload-function-app-local-settings"></a>Carregar definições locais da aplicação de funções

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Procure e selecione o comando **Azure Functions: Upload local settings** (Funções do Azure: Carregar definições locais).

1. Quando lhe for pedido, forneça as seguintes informações.

    | Nome | Valor |
    |---|---|
    | Ficheiro de definições locais | local.settings.json |
    | Subscrição | Selecione a sua subscrição |
    | Aplicação de funções | Selecione a aplicação de funções implementada anteriormente |

As definições locais são carregadas para a aplicação de funções no Azure. Se lhe for pedido para substituir as definições existentes, selecione **Sim para tudo**.


### <a name="enable-app-service-authentication"></a>Ativar a Autenticação do Serviço de Aplicações

A Autenticação do Serviço de Aplicações suporta a autenticação com o Azure Active Directory, Facebook, Twitter, conta Microsoft e Google.

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Procure e selecione o comando **Azure Functions: Open in portal** (Funções do Azure: Abrir no portal).

1. Selecione a subscrição e o nome da aplicação de funções para abri-la no portal do Azure.

1. Na aplicação de função que foi aberta no portal, localizar o separador funcionalidades da **Plataforma,** selecionar **Autenticação/Autorização.**

1. Ative a **Autenticação do Serviço de Aplicações**.

1. Em **Ação a tomar quando o pedido não é autenticado**, selecione "Iniciar sessão com {fornecedor de autenticação selecionado anteriormente}".

1. Em **URLs de Redirecionamento Externo Permitidos**, introduza o URL do ponto final Web primário da conta de armazenamento que anotou anteriormente.

1. Siga a documentação para o fornecedor de início de sessão da sua preferência para concluir a configuração.

    - [Azure Active Directory](../app-service/configure-authentication-provider-aad.md)
    - [Facebook](../app-service/configure-authentication-provider-facebook.md)
    - [Twitter](../app-service/configure-authentication-provider-twitter.md)
    - [Conta Microsoft](../app-service/configure-authentication-provider-microsoft.md)
    - [Google](../app-service/configure-authentication-provider-google.md)

### <a name="update-the-web-app"></a>Atualizar a aplicação Web

1. No portal do Azure, navegue para a página de descrição geral da aplicação de funções.

1. Copie o URL da aplicação de funções.

    ![Obter o URL](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. No VS Code, abra **index.html** e substitua o valor de `apiBaseUrl` pelo URL da aplicação de funções.

1. A aplicação pode ser configurada com autenticação com o Azure Active Directory, Facebook, Twitter, conta Microsoft ou Google. Selecione o fornecedor de autenticação que irá utilizar ao definir o valor de `authProvider`.

1. Guarde o ficheiro.

### <a name="deploy-the-web-application-to-blob-storage"></a>Implementar a aplicação Web no armazenamento de blobs

A aplicação Web será alojada através da funcionalidade de sites estáticos do Armazenamento de Blobs do Azure.

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Procure e selecione o comando **Azure Storage: Deploy to Static Website** (Armazenamento do Azure: Implementar no Site Estático).

1. Introduza os seguintes valores:

    | Name | Valor |
    |---|---|
    | Subscrição | Selecione a sua subscrição |
    | Conta de armazenamento | Selecione a conta de armazenamento que criou anteriormente |
    | Pasta para implementar | **Selecione Procurar** e selecionar a pasta *de conteúdo* |

Os ficheiros na pasta de *conteúdo* devem agora ser implantados no website estático.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Ativar a partilha de recursos transversais à origem (CORS) na aplicação de funções

Embora exista uma definição de CORS em **local.settings.json**, esta não é propagada para a aplicação de funções no Azure. Tem de defini-la em separado.

1. Abra a aplicação de funções no portal do Azure.

1. No separador **funcionalidades da Plataforma,** selecione **CORS**.

    ![Localizar o CORS](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. Na secção *Origens Permitidas,* adicione uma entrada com o *ponto final primário* do site estático como o valor (remova o */* rasto).

1. Para que o SignalR JavaScript SDK ligue para a sua aplicação de função a partir de um browser, o suporte para credenciais em CORS deve ser ativado. Selecione a caixa de verificação "Ativar o controlo de acesso-permitir-credenciais".

    ![Ativar o acesso-control-allow-credenciais](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Clique em **Guardar** para manter as definições de CORS.

### <a name="try-the-application"></a>Experimentar a aplicação

1. Num browser, navegue para o ponto final Web primário da conta de armazenamento.

1. Selecione **Iniciar sessão** para autenticar com o fornecedor de autenticação selecionado.

1. Envie mensagens públicas através da caixa de chat principal.

1. Envie mensagens privadas ao clicar num nome de utilizador no histórico de chat. Apenas o destinatário selecionado receberá as mensagens.

Parabéns! Implementou uma aplicação de chat sem servidor em tempo real!

![Demonstração](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados neste tutorial, elimine o grupo de recursos no portal do Azure.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as Funções do Azure com o Azure SignalR Service. Leia mais sobre a criação de aplicações sem servidor em tempo real com enlaces do SignalR Service para as Funções do Azure.

> [!div class="nextstepaction"]
> [Construir aplicativos em tempo real com funções Azure](signalr-concept-azure-functions.md)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsauth)