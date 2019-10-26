---
title: Guia de início rápido para aprender a usar o serviço de Signaler do Azure com o ASP.NET
description: Um guia de início rápido para usar o serviço de sinalizador do Azure para criar uma sala de chat com o ASP.NET Framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: 8dfb74fc941a9bb6d235093287ac47983c0e0564
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931828"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Início rápido: criar uma sala de chat com o ASP.NET e o serviço Signalr

O serviço de Signaler do Azure é baseado no [signalr para ASP.NET Core 2,0](https://docs.microsoft.com/aspnet/core/signalr/introduction), que **não** é 100% compatível com o signalr ASP.net. O serviço de sinalizador do Azure reimplementou o protocolo de dados do Signalr ASP.NET com base nas tecnologias de ASP.NET Core mais recentes. Ao usar o serviço de Signaler do Azure para o Signalr ASP.NET, não há mais suporte para alguns recursos do Signalr ASP.NET, por exemplo, o Azure Signalr não reproduz mensagens quando o cliente se reconecta. Além disso, o transporte de quadro contínuo e o JSONP não têm suporte. Algumas alterações de código e uma versão adequada das bibliotecas dependentes são necessárias para fazer com que o aplicativo Signalr ASP.NET funcione com o serviço Signalr. 

Consulte o [documento diferenças de versão](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) para obter uma lista completa de comparação de recursos entre o signalr ASP.net e o signalr ASP.NET Core.

Neste guia de início rápido, você aprenderá a começar a usar o ASP.NET e o serviço de Signaler do Azure para um [aplicativo de sala de chat](./signalr-quickstart-dotnet-core.md)semelhante.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [4.6.1 .NET](https://www.microsoft.com/net/download/windows)
* [ASP.NET Signalr 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Não há suporte para o modo sem *servidor* para aplicativos signalr ASP.net. Sempre use o *padrão* ou o *clássico* para a instância do serviço de signaler do Azure.

Você também pode criar recursos do Azure usados neste guia de início rápido com a [criação de um script de serviço signalr](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Enquanto o serviço está a ser implementado, vamos passar para trabalhar com código. Clone a [aplicação de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), defina a cadeia de ligação do Serviço SignalR e execute a aplicação localmente.

1. Abra uma janela de terminal do git. Alterar para uma pasta em que quer clonar o projeto de exemplo.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Configurar e executar o aplicativo Web sala de chat

1. Inicie o Visual Studio e abra a solução na pasta *ASPNET-Samples/ChatRoom/* do repositório clonado.

1. No navegador em que o portal do Azure está aberto, localize e selecione a instância que você criou.

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

1. Agora, defina a cadeia de conexão no arquivo Web. config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. Em *Startup.cs*, em vez de chamar `MapSignalR()`, você precisa chamar `MapAzureSignalR({your_applicationName})` e passar a cadeia de conexão para fazer com que o aplicativo se conecte ao serviço, em vez de hospedar o signalr por si só. Substitua `{YourApplicationName}` pelo nome do seu aplicativo. Esse nome é um nome exclusivo para distinguir esse aplicativo de outros aplicativos. Você pode usar `this.GetType().FullName` como o valor.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Você também precisa referenciar o SDK do serviço antes de usar essas APIs. Abrir as **ferramentas | Gerenciador de pacotes NuGet | Console do Gerenciador de pacotes** e comando executar:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Além dessas alterações, tudo o mais permanece o mesmo, você ainda pode usar a interface de Hub com a qual você já está familiarizado para escrever lógica de negócios.

    > [!NOTE]
    > Na implementação, um ponto de extremidade `/signalr/negotiate` é exposto para negociação pelo SDK do serviço de Signaler do Azure. Ele retornará uma resposta de negociação especial quando os clientes tentarem se conectar e redirecionar clientes para o ponto de extremidade de serviço definido na cadeia de conexão.

1. Pressione **F5** para executar o projeto no modo de depuração. Você pode ver que o aplicativo é executado localmente. Em vez de hospedar um tempo de execução de sinal próprio pelo próprio aplicativo, ele agora se conecta ao serviço de Signaler do Azure.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , escreva o nome do grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

   
![Eliminar](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo recurso do serviço de Signaler do Azure e o utilizou com um aplicativo Web ASP.NET. Em seguida, saiba como desenvolver aplicativos em tempo real usando o serviço de Signalr do Azure com o ASP.NET Core.

> [!div class="nextstepaction"]
> [Serviço do Azure Signalr com ASP.NET Core](./signalr-quickstart-dotnet-core.md)
