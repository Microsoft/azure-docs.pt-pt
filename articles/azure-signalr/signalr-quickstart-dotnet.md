---
title: Início rápido para saber como utilizar o serviço Azure SignalR com ASP.NET
description: Guia de introdução para utilizar o serviço Azure SignalR para criar uma sala de bate-papo com a estrutura do ASP.NET.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: cda1eddc5fa40e97038274cf9b4c3c2cb9552871
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154506"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Início rápido: Criar uma sala de bate-papo com ASP.NET e o serviço SignalR

Serviço Azure SignalR baseia [SignalR para ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), que é **não** 100% compatível com o SignalR de ASP.NET. Serviço Azure SignalR implementou novamente o protocolo de dados do SignalR de ASP.NET com base no technoledges mais recente do ASP.NET Core. Quando utilizar o serviço Azure SignalR para ASP.NET SignalR, algumas funcionalidades SignalR de ASP.NET já não são suportadas, por exemplo, Azure SignalR reproduzir não mensagens quando a reconexão do cliente. Além disso, o transporte do quadro para sempre e JSONP não são suportadas. Algumas alterações de código e a versão apropriada do bibliotecas dependentes são necessárias para tornar a aplicação de ASP.NET SignalR trabalhar com o serviço SignalR. 

Consulte a [doc de diferenças de versão](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) para obter uma lista completa de comparação de funcionalidades entre o SignalR de ASP.NET e o SignalR do ASP.NET Core.

Neste início rápido, ficará a saber como começar com o ASP.NET e o serviço Azure SignalR para um semelhante [aplicação de sala de bate-papo](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [SignalR de ASP.NET 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Sem servidor* modo não é suportado para aplicativos do SignalR de ASP.NET. Utilize sempre *predefinido* ou *clássico* para a instância do serviço Azure SignalR.

Também pode criar recursos do Azure utilizados neste início rápido com [criar um script de serviço SignalR](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Enquanto o serviço está a ser implementado, vamos passar para trabalhar com código. Clone a [aplicação de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), defina a cadeia de ligação do Serviço SignalR e execute a aplicação localmente.

1. Abra uma janela de terminal do git. Alterar para uma pasta em que quer clonar o projeto de exemplo.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Configurar e executar a aplicação web de sala de bate-papo

1. Inicie o Visual Studio e abra a solução no *aspnet-amostras/ChatRoom/* pasta do repositório clonado.

1. No browser em que é aberto o portal do Azure, localize e selecione a instância que criou.

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

1. Agora, defina a cadeia de ligação no ficheiro Web. config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. Na *Startup.cs*, em vez de chamar `MapSignalR()`, precisa chamar `MapAzureSignalR({your_applicationName})` e passar na cadeia de ligação para tornar o aplicativo ligar ao serviço em vez de alojamento SignalR por si só. Substitua `{YourApplicationName}` para o nome da sua aplicação. Este nome é um nome exclusivo para distinguir esta aplicação a partir de outras aplicações. Pode usar `this.GetType().FullName` como o valor.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Também tem de referenciar o SDK do serviço antes de utilizar estas APIs. Abra o **ferramentas | Gestor de pacotes NuGet | Consola do Gestor de pacotes** e execute o comando:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Além dessas alterações, tudo o resto permanece igual, pode continuar a utilizar a interface de hub que já estiver familiarizado com a escrever lógica de negócio.

    > [!NOTE]
    > Na implementação de um ponto de extremidade `/signalr/negotiate` é exposto para negociação pelo SDK do serviço Azure SignalR. Irá devolver uma resposta de negociação especial quando os clientes tentam ligar e redirecionar os clientes para o ponto final de serviço definido na cadeia de ligação.

1. Prima **F5** para executar o projeto no modo de depuração. Pode ver a aplicação é executada localmente. Em vez de hospedar um tempo de execução do SignalR ao próprio aplicativo, ele agora se conecta ao serviço Azure SignalR.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

   
![Eliminar](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo recurso do serviço Azure SignalR e Use com uma aplicação web ASP.NET. Em seguida, saiba como desenvolver aplicativos em tempo real usando o serviço Azure SignalR com o ASP.NET Core.

> [!div class="nextstepaction"]
> [Serviço Azure SignalR ASP.NET Core](./signalr-quickstart-dotnet-core.md)
