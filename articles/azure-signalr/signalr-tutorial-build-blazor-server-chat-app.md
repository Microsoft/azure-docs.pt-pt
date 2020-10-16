---
title: 'Tutorial: Construa uma aplicação de chat Do Blazor Server - Azure SignalR'
description: Neste tutorial, você aprende a construir e modificar uma aplicação Do Blazor Server com O Serviço Azure SignalR
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 1a75c083015d1f10a3ed3dba15480430747756eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90525021"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Tutorial: Construa uma aplicação de chat do Blazor Server

Este tutorial mostra-lhe como construir e modificar uma aplicação Do Servidor Blazor. Vai aprender a:

> [!div class="checklist"]
> * Construa uma sala de chat simples com a aplicação Blazor Server.
> * Modificar os componentes da Navalha.
> * Utilize o tratamento de eventos e a ligação de dados em componentes.
> * Implementação rápida para o Azure App Service em Visual Studio.
> * Migrar o SignalR local para o Serviço Azure SignalR.

## <a name="prerequisites"></a>Pré-requisitos
* Instalar [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (versão >= 3.0.100)
* Instalar [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (Versão >= 16.3)
> A versão preview do Visual Studio 2019 também funciona que está a ser lançada com o mais recente modelo de aplicação do Blazor Server direcionado para a versão mais recente do .Net Core.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Construa uma sala de chat local na aplicação Blazor Server

A partir da versão 16.2.0 do Visual Studio 2019, o Serviço Azure SignalR é um processo de publicação de aplicações web e gerir as dependências entre a aplicação web e o serviço SignalR seria muito mais conveniente. Pode experienciar trabalhar no SignalR local em ambiente local dev e trabalhar no Serviço Azure SignalR para o Serviço de Aplicações Azure ao mesmo tempo, sem alterações de código.

1. Criar uma app de chat Blazor
   
   No Visual Studio, escolha Criar um novo projeto -> Blazor App -> (nomeie a app e escolha uma pasta) -> Blazor Server App. Certifique-se de que já instalou .NET Core SDK 3.0+ para permitir que o Visual Studio reconheça corretamente a estrutura-alvo.

   [![blazor-chat-create ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Ou executar cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Adicione um `BlazorChatSampleHub.cs` ficheiro para implementar para o `Hub` chat.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Adicione um ponto final para o hub em `Startup.Configure()` .
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Instale `Microsoft.AspNetCore.SignalR.Client` o pacote para utilizar o cliente SignalR.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Criar `ChartRoom.razor` em pasta para implementar o cliente `Pages` SignalR. Siga os passos abaixo ou simplesmente copie o [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Adicione link de página e referência.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Adicione código ao novo cliente SignalR para enviar e receber mensagens.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Adicione antes a parte de renderização `@code` para a UI interagir com o cliente SignalR.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Atualização `NavMenu.razor` para inserir um menu de entrada para a sala de chat sob o mesmo `NavMenuCssClass` descanso.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Atualização `site.css` para otimizar para vistas de bolhas de área de gráfico. Apêndice abaixo do código no final.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Clique em <kbd>F5</kbd> para executar a aplicação. Poderá conversar como abaixo.

   [![blazor-chat ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publicar no Azure

   Até agora, a App Blazor está a trabalhar no SignalR local e quando é implantada no Azure App Service, é sugerido utilizar o [Serviço Azure SignalR](https://docs.microsoft.com/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) que permite aumentar uma aplicação do Blazor Server para um grande número de ligações signalr simultâneas. Além disso, o alcance global do serviço SignalR e os centros de dados de alto desempenho ajudam significativamente na redução da latência devido à geografia.

> [!IMPORTANT]
> Na aplicação Blazor Server, os estados da UI são mantidos no lado do servidor, o que significa que o servidor pegajoso é necessário neste caso. Se houver um servidor de aplicações único, o servidor pegajoso é assegurado pelo design. No entanto, se existirem vários servidores de aplicações, existe a possibilidade de a negociação e ligação do cliente poderem ir para diferentes servidores e levar a erros de UI na aplicação Blazor. Por isso, tem de ativar o servidor pegajoso como abaixo em `appsettings.json` :
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Clique no projeto e navegue para `Publish` .

   * Alvo: Azure
   * Alvo específico: Todos os tipos de Serviço de **Aplicações Azure** são suportados.
   * Serviço de Aplicações: crie um novo ou selecione o serviço de aplicações existente.

   [![blazor-chat-profile ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Adicionar dependência do serviço Azure SignalR

   Após a publicação do perfil criado, pode ver uma mensagem recomendada em **Dependências de Serviços.** Clique em **Configurar** para criar o novo ou selecionar o Serviço Azure SignalR existente no painel.

   [![blazor-chat-dependência ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   A dependência do serviço fará coisas abaixo para ativar a sua aplicação automaticamente para o Serviço Azure SignalR quando estiver no Azure.

   * Atualização [`HostingStartupAssembly`](https://docs.microsoft.com/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) para utilizar o Serviço Azure SignalR.
   * Adicione referência de pacote nuget de serviço Azure SignalR.
   * Atualize as propriedades do perfil para salvar as definições de dependência.
   * A loja de segredos de configuração depende da sua escolha.
   * Adicione `appsettings` a configuração para tornar o seu alvo de aplicação selecionado Serviço Azure SignalR.

   [![blazor-chat-dependência-resumo ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Publicar a aplicação

   Agora está pronto para publicar. E irá auto-navegador a página depois de publicar completações. 
   > [!NOTE]
   > Pode não funcionar imediatamente na primeira página de visita devido à implementação do Serviço de Aplicações Azure iniciar a latência e tentar refrescar a página para dar algum atraso.
   > Além disso, pode utilizar o modo de depurador de navegador com <kbd>F12</kbd> para validar o tráfego já redirecionado para o Serviço Azure SignalR.

   [![blazor-chat-azure ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Outro tópico: Ativar o Serviço Azure SignalR no desenvolvimento local

1. Adicionar referência ao Azure SignalR SDK

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Adicione uma chamada para o Serviço Azure SignalR em `Startup.ConfigureServices()` .

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Configure Serviço Azure SignalR `ConnectionString` dentro ou com ferramenta De Gestor `appsetting.json` [Secreto](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=visual-studio#secret-manager)

> [!NOTE]
> O passo 2 pode ser substituído utilizando [`HostingStartupAssembly`](https://docs.microsoft.com/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) o SignalR SDK.
> 
> 1. Adicionar configuração para ligar o Serviço Azure SignalR em `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Atribua o conjunto de arranque de hospedagem para utilizar o Azure SignalR SDK. Editar `launchSettings.json` e adicionar uma configuração como abaixo `environmentVariables` dentro .
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Tendo problemas? Deixe-nos saber.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados neste tutorial, elimine o grupo de recursos no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Construa uma sala de chat simples com a aplicação Blazor Server.
> * Modificar os componentes da Navalha.
> * Utilize o tratamento de eventos e a ligação de dados em componentes.
> * Implementação rápida para o Azure App Service em Visual Studio.
> * Migrar o SignalR local para o Serviço Azure SignalR.

Leia mais sobre alta disponibilidade.
> [!div class="nextstepaction"]
> [Resiliência e recuperação após desastre](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Recursos adicionais

* [ASP.NET Core Blazor](https://docs.microsoft.com/aspnet/core/blazor)
