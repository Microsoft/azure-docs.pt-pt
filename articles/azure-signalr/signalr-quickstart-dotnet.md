---
title: Desenvolver com ASP.NET - Serviço Azure SignalR
description: Um quickstart para usar o Serviço Azure SignalR para criar uma sala de chat com ASP.NET estrutura.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: c39ef505b0cea0ad0c03b81683db8441077cd0d2
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874547"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Quickstart: Criar uma sala de chat com ASP.NET e serviço SignalR

O Serviço Azure SignalR baseia-se no [SignalR para ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), que **não** é 100% compatível com ASP.NET SignalR. O Serviço Azure SignalR reen implementou ASP.NET protocolo de dados SignalR com base nas mais recentes tecnologias core ASP.NET. Ao utilizar o Serviço Azure SignalR para ASP.NET SignalR, algumas ASP.NET as funções signalR já não são suportadas, por exemplo, o Azure SignalR não reproduz mensagens quando o cliente se reconecta. Além disso, o transporte Forever Frame e o JSONP não são suportados. Algumas alterações de código e a versão adequada das bibliotecas dependentes são necessárias para fazer ASP.NET aplicação SignalR funcionar com o Serviço SignalR.

Consulte as diferenças de versão doc para uma lista completa de comparação de [funcionalidades](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) entre ASP.NET SignalR e ASP.NET Core SignalR.

Neste arranque rápido, você aprenderá a começar com o serviço ASP.NET e Azure SignalR para uma [aplicação](./signalr-quickstart-dotnet-core.md)semelhante da Chat Room .


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [sinaleiro ASP.NET 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsnet)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsnet)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

O modo *serverless* não é suportado para aplicações ASP.NET SignalR. Utilize sempre *o Predefinido* ou *Clássico* para a instância do Serviço Azure SignalR.

Também pode criar recursos Azure utilizados neste arranque rápido com [o script Criar um serviço SignalR](scripts/signalr-cli-create-service.md).

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsnet)

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Enquanto o serviço está a ser implementado, vamos passar para trabalhar com código. Clone a [aplicação de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), defina a cadeia de ligação do Serviço SignalR e execute a aplicação localmente.

1. Abra uma janela de terminal do git. Alterar para uma pasta em que quer clonar o projeto de exemplo.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsnet)

## <a name="configure-and-run-chat-room-web-app"></a>Configure e execute o aplicativo web Chat Room

1. Inicie o Visual Studio e abra a solução na *pasta aspnet-samples/ChatRoom/* do repositório clonado.

1. No navegador onde o portal Azure é aberto, encontre e selecione o caso que criou.

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

1. Agora coloque a cadeia de ligação no ficheiro *web.config.*

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. Em *Startup.cs*, em vez de `MapSignalR()` ligar, é necessário ligar e passar no `MapAzureSignalR({YourApplicationName})` fio de ligação para fazer a aplicação ligar-se ao serviço em vez de hospedar o SignalR por si só. Substitua `{YourApplicationName}` o nome da sua candidatura. Este nome é um nome único para distinguir esta aplicação das suas outras aplicações. Pode usar `this.GetType().FullName` como valor.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Também precisa de fazer referência ao serviço SDK antes de utilizar estes APIs. Abra as **Ferramentas ! Gestor de pacotes NuGet Consola do gestor de pacotes** e comando de execução:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Além destas alterações, tudo o resto permanece o mesmo, ainda pode usar a interface do hub que já está familiarizado para escrever lógica de negócio.

    > [!NOTE]
    > Na implementação, um ponto final `/signalr/negotiate` é exposto para negociação pela Azure SignalR Service SDK. Devolverá uma resposta de negociação especial quando os clientes tentarem ligar e redirecionar os clientes para o ponto final de serviço definido na cadeia de ligação.

1. Prima <kbd>F5</kbd> para executar o projeto em modo de depuragem. Pode ver que a aplicação é feita localmente. Em vez de hospedar um tempo de funcionamento signalR por aplicação em si, ele agora se conecta ao Serviço Azure SignalR.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsnet)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsnet)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um novo recurso do Serviço Azure SignalR e utilizou-o com uma aplicação web ASP.NET. Em seguida, aprenda a desenvolver aplicações em tempo real usando o Serviço Azure SignalR com ASP.NET Core.

> [!div class="nextstepaction"]
> [Serviço Azure SignalR com ASP.NET Core](./signalr-quickstart-dotnet-core.md)
