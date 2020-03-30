---
title: Desenvolver com ASP.NET - Serviço De Sinalização Azure
description: Um arranque rápido para a utilização do Serviço De Sinalização Azure para criar uma sala de chat com ASP.NET enquadramento.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74158175"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Quickstart: Criar uma sala de chat com serviço ASP.NET e SignalR

O Serviço De Sinalização Azure baseia-se no [SignalR para ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), que **não** é 100% compatível com ASP.NET SignalR. O Serviço De Sinalização Azure reimplementou o protocolo de dados ASP.NET SignalR com base nas mais recentes tecnologias core ASP.NET. Ao utilizar o Serviço De Sinalização Azure para ASP.NET SignalR, algumas funcionalidades ASP.NET SignalR deixaram de ser suportadas, por exemplo, o Azure SignalR não reproduz mensagens quando o cliente se reconecta. Além disso, o transporte forever frame e jSONP não são suportados. Algumas alterações de código e versão adequada das bibliotecas dependentes são necessárias para que ASP.NET aplicação SignalR funcione com o SignalR Service. 

Consulte as diferenças de [versão do c.](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) para obter uma lista completa de comparação de funcionalidades entre ASP.NET SignalR e ASP.NET Core SignalR.

Neste arranque rápido, você aprenderá a começar com o serviço de sinalização ASP.NET e Azure para uma [aplicação](./signalr-quickstart-dotnet-core.md)de Chat Room semelhante .


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.net 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET Sinalizador 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

O modo *sem servidor* estoque não é suportado para aplicações ASP.NET SignalR. Utilize sempre *o Padrão* ou *o Clássico* para a instância de serviço de sinalização Azure.

Também pode criar recursos Azure utilizados neste quickstart com [o script Create a SignalR Service](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Enquanto o serviço está a ser implementado, vamos passar para trabalhar com código. Clone a [aplicação de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), defina a cadeia de ligação do Serviço SignalR e execute a aplicação localmente.

1. Abra uma janela de terminal do git. Alterar para uma pasta em que quer clonar o projeto de exemplo.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Configure e executar app web Chat Room

1. Inicie o Estúdio Visual e abra a solução nas *amostras de aspnet/ChatRoom/pasta* do repositório clonado.

1. No navegador onde o portal Azure é aberto, encontre e selecione a instância que criou.

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

1. Agora, coloque a cadeia de ligação no ficheiro web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. Na *Startup.cs*, `MapSignalR()`em vez de `MapAzureSignalR({your_applicationName})` ligar, é necessário ligar e passar em cadeia de ligação para que a aplicação se ligue ao serviço em vez de hospedar o SignalR por si só. Substitua-o `{YourApplicationName}` pelo nome da sua candidatura. Este nome é um nome único para distinguir esta aplicação das suas outras aplicações. Pode usar `this.GetType().FullName` como valor.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Também precisa de fazer referência ao serviço SDK antes de utilizar estas APIs. Abra as **Ferramentas / NuGet Package Manager [ Gestor de Pacotes NuGet ] Consola de gestor de pacotes** e comando de execução:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Para além destas mudanças, tudo o resto permanece o mesmo, ainda podes usar a interface do hub que já conheces para escrever lógica de negócio.

    > [!NOTE]
    > Na implementação, `/signalr/negotiate` um ponto final é exposto para negociação pelo Serviço De Sinalização Azure SDK. Devolverá uma resposta de negociação especial quando os clientes tentarem ligar e redirecionar os clientes para o ponto final de serviço definido na cadeia de ligação.

1. Pressione **f5** para executar o projeto em modo dedepura. Pode ver que a aplicação é feita localmente. Em vez de hospedar um tempo de funcionamento signalR por aplicação em si, agora liga-se ao Serviço De Sinalização Azure.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

   
![Eliminar](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um novo recurso do Serviço De Sinalização Azure e utilizou-o com uma aplicação web ASP.NET. Em seguida, aprenda a desenvolver aplicações em tempo real utilizando o Serviço De Sinalização Azure com ASP.NET Core.

> [!div class="nextstepaction"]
> [Serviço de Sinalizador Azure com núcleo de ASP.NET](./signalr-quickstart-dotnet-core.md)
