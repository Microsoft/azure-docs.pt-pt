---
title: Início rápido para saber como utilizar o serviço Azure SignalR
description: Um início rápido para utilizar o Azure SignalR Service para criar uma sala de chat com aplicações ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 3dc893ea10e47e867110f674a458498a6bd24a4f
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560697"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Início rápido: Criar uma sala de bate-papo com o serviço SignalR


O Serviço Azure SignalR é um serviço do Azure que ajuda os programadores a facilmente criar aplicações web com funcionalidades em tempo real. Este serviço é baseado em [SignalR para ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

Este artigo mostra-lhe como começar a utilizar o Azure SignalR Service. Neste início rápido, irá criar uma aplicação de chat, utilizando uma aplicação web de MVC do ASP.NET Core. Esta aplicação irá fazer uma ligação ao seu recurso do Azure SignalR Service para ativar as atualizações de conteúdos em tempo real. Irá alojar a aplicação web localmente e ligue-se com vários clientes de browser. Cada cliente poderá enviar atualizações de conteúdos para todos os outros clientes. 

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. Uma opção é [Visual Studio Code](https://code.visualstudio.com/), que está disponível no Windows, macOS e plataformas Linux.

O código deste tutorial está disponível para transferência no [repositório do GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Além disso, pode criar os recursos do Azure utilizados neste início rápido ao seguir [criar um script de serviço SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Instalar o [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Transfira ou clone a [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) repositório do GitHub. 

## <a name="create-an-azure-signalr-resource"></a>Criar um recurso do Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Nesta secção, vai utilizar o [.NET Core interface de linha de comandos (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um projeto de aplicação web ASP.NET Core MVC. A vantagem de utilizar a CLI do .NET Core ao longo do Visual Studio é que ele está disponível para todas as plataformas de Linux, macOS e Windows. 

1. Crie uma pasta para o seu projeto. Este início rápido utiliza a *E:\Testing\chattest* pasta.

2. Na nova pasta, execute o seguinte comando para criar o projeto:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Adicionar o Secret Manager ao projeto

Nesta secção, irá adicionar os [ferramenta Gerenciador de segredo](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Gerenciador de segredo armazena dados confidenciais para projetos de programação fora da sua árvore de projeto. Esta abordagem ajuda a impedir a partilha acidental de segredos de aplicação no código-fonte.

1. Abra o ficheiro *.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Também adicionar uma `UserSecretsId` elemento, conforme mostrado no código a seguir para *chattest.csproj*e guarde o ficheiro.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Adicionar o Azure SignalR à aplicação Web

1. Adicionar uma referência para o `Microsoft.Azure.SignalR` pacote NuGet ao executar o seguinte comando:

        dotnet add package Microsoft.Azure.SignalR

2. Execute o seguinte comando para restaurar pacotes para o seu projeto:

        dotnet restore

3. Adicione um segredo denominado *Azure:SignalR:ConnectionString* ao Secret Manager. 

    Este segredo irá conter a cadeia de ligação para aceder ao seu recurso do SignalR Service. *Azure: SignalR:ConnectionString* é a chave de configuração predefinida parecido com o SignalR para estabelecer uma ligação. Substitua o valor no comando seguinte a cadeia de ligação para o seu recurso de serviço SignalR.

    Tem de executar este comando no mesmo diretório como o *. csproj* ficheiro.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Gestor de segredo será utilizado apenas para a aplicação web de teste, enquanto ele estiver alojado localmente. Num tutorial posterior, vai implementar a aplicação de web chat para o Azure. Depois de implementar a aplicação web para o Azure, usará uma definição da aplicação em vez de armazenar a cadeia de ligação com o Gestor de segredo.

    Este segredo é acessado com a API de configuração. Dois pontos (:) funciona no nome da configuração com a API de configuração nas plataformas suportadas. Ver [configuração pelo ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Abra *Startup.cs* e atualize o método `ConfigureServices` para utilizar o Azure SignalR ao chamar o método `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Por não passar um parâmetro para `AddAzureSignalR()`, esse código usa a chave de configuração predefinida para a cadeia de ligação de recurso de serviço SignalR. É a chave de configuração predefinida *do Azure: SignalR:ConnectionString*.

5. Também no *Startup.cs*, atualize o método `Configure` ao substituir a chamada para `app.UseStaticFiles()` pelo seguinte código e guarde o ficheiro.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Adicionar a classe Hub

Um hub SignalR, é um importante componente que expõe um conjunto de métodos que pode ser chamada do cliente. Nesta secção, vai definir uma classe hub com dois métodos: 

* `Broadcast`: Este método transmite uma mensagem a todos os clientes.
* `Echo`: Este método envia uma mensagem para o chamador.

Ambos os métodos usam o `Clients` interface que fornece o SDK de SignalR do ASP.NET Core. Essa interface fornece acesso a todos os clientes ligados, pelo que pode enviar conteúdo para os seus clientes.

1. No diretório do projeto, adicione uma pasta nova designada *Hub*. Adicione um ficheiro de código de hub novo, chamado *Chat.cs*, à pasta nova.

2. Adicione o seguinte código para *Chat.cs* para definir a sua classe de hub e guarde o ficheiro. 

    Se tiver utilizado um nome de projeto diferente de *chattest*, atualize o espaço de nomes desta classe.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Adicionar a interface de cliente para a aplicação web

A interface de utilizador do cliente para esta aplicação de sala de bate-papo consistirá em HTML e JavaScript num arquivo chamado *Index. HTML* no *wwwroot* diretório.

Cópia a *Index. HTML* arquivo, o *css* pasta e o *scripts* pasta a partir do *wwwroot* pasta do [exemplos repositório](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Cole-os no seu projeto *wwwroot* pasta.

Eis o código principal do *Index*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

O código na *Index. HTML* chamadas `HubConnectionBuilder.build()` para estabelecer uma ligação de HTTP para o recurso do Azure SignalR.

Se a ligação for bem-sucedida, é transmitida para `bindConnectionMessage`, que adiciona processadores de eventos para envios de conteúdos de entrada para o cliente. 

`HubConnection.start()` inicia a comunicação com o hub. Em seguida, `onConnected()` adiciona os manipuladores de eventos do botão. Estes processadores utilizam a ligação para permitir que este cliente envie atualizações de conteúdos para todos os clientes ligados.

## <a name="add-a-development-runtime-profile"></a>Adicionar um perfil de tempo de execução de desenvolvimento

Nesta secção, irá adicionar um ambiente de tempo de execução de desenvolvimento para o ASP.NET Core. Para obter mais informações, consulte [trabalhar com vários ambientes no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Crie uma pasta denominada *propriedades* no seu projeto.

2. Adicionar um novo ficheiro designado *launchSettings.json* para a pasta, com o seguinte conteúdo e guarde-o.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Para criar a aplicação com a CLI do .NET Core, execute o seguinte comando na shell de comandos:

        dotnet build

2. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

    A aplicação será hospedada localmente na porta 5000, conforme configurado no nosso perfil de tempo de execução de desenvolvimento:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Abra duas janelas do browser. Na cada browser, aceda a `http://localhost:5000`. Lhe for pedido para introduzir o seu nome. Introduza um nome de cliente para clientes e teste, enviar por push o conteúdo da mensagem entre ambos os clientes com o **enviar** botão.

    ![Exemplo de um bate-papo de grupo do Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se irá continuar para o próximo tutorial, pode manter os recursos criados neste início rápido e reutilizá-los.

Se tiver terminado com o aplicativo de exemplo de início rápido, pode eliminar os recursos do Azure que criou neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A eliminar um grupo de recursos é irreversível e inclui todos os recursos nesse grupo. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que quer manter, pode eliminar cada recurso individualmente do respetivo painel em vez de eliminar o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

Na **filtrar por nome** texto, escreva o nome do seu grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No seu grupo de recursos na lista de resultados, selecione as reticências (**...** ) > **Eliminar grupo de recursos**.

   
![Seleções para eliminar um grupo de recursos](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


É-lhe pedido que confirme a eliminação do grupo de recursos. Introduza o nome do seu grupo de recursos para confirmar e selecione **eliminar**.
   
Após alguns instantes, o grupo de recursos e todos os respetivos recursos são eliminados.



## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo recurso do serviço Azure SignalR. Em seguida, utilizou-lo com uma aplicação web ASP.NET Core para enviar atualizações de conteúdo em tempo real para vários clientes ligados. Para saber mais sobre como utilizar o serviço Azure SignalR, avance para o tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Azure SignalR Service authentication](./signalr-concept-authenticate-oauth.md) (Autenticação do Azure SignalR Service)


