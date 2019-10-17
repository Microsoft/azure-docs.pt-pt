---
title: Habilitar sincronização offline para seu aplicativo de Plataforma Universal do Windows (UWP) com aplicativos móveis | Microsoft Docs
description: Saiba como usar um aplicativo móvel do Azure para armazenar em cache e sincronizar dados offline no seu aplicativo de Plataforma Universal do Windows (UWP).
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 1f15bd86debd98104a408c00650f5acfab94c526
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388581"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Ativar a sincronização offline da aplicação do Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar suporte offline a um aplicativo Plataforma Universal do Windows (UWP) usando um back-end do aplicativo móvel do Azure. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, essas alterações serão sincronizadas com o back-end remoto.

Neste tutorial, você atualiza o projeto de aplicativo UWP do tutorial [criar um aplicativo do Windows] para dar suporte aos recursos offline dos aplicativos móveis do Azure. Se você não usar o projeto baixado do servidor de início rápido, deverá adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="requirements"></a>Requisitos  
Este tutorial requer os seguintes pré-requisitos:

* Visual Studio 2013 em execução no Windows 8.1 ou posterior.
* Conclusão de [criar um aplicativo do Windows][criar um aplicativo do Windows].
* [Armazenamento do SQLite dos serviços móveis do Azure][sqlite store nuget]
* [SQLite para desenvolvimento de Plataforma Universal do Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar o aplicativo cliente para dar suporte a recursos offline
Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você Inicializa uma interface [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable]. O SQLite é usado como o repositório local no dispositivo.

1. Instale o [tempo de execução do SQLite para o plataforma universal do Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. No Visual Studio, abra o Gerenciador de pacotes NuGet para o projeto de aplicativo UWP que você concluiu no tutorial [criar um aplicativo do Windows] .
    Procure e instale o pacote NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
3. Em Gerenciador de Soluções, clique com o botão direito do mouse em **referências** > **Adicionar referência...** > **extensões** **Universal do Windows** > e habilite o **SQLite para plataforma universal do Windows** e o **Visual C++ 2015 Tempo de execução para aplicativos Plataforma Universal do Windows**.

    ![Adicionar referência do SQLite UWP][1]
4. Abra o arquivo MainPage.xaml.cs e remova a marca de comentário da definição `#define OFFLINE_SYNC_ENABLED`.
5. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo cliente. O aplicativo funciona da mesma forma que antes você habilitou a sincronização offline. No entanto, o banco de dados local agora é populado com aqueles que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo para desconectar-se do back-end
Nesta seção, você interrompe a conexão com o back-end do aplicativo móvel para simular uma situação offline. Quando você adiciona itens de dados, seu manipulador de exceção informa que o aplicativo está no modo offline. Nesse estado, novos itens adicionados no repositório local e serão sincronizados com o back-end do aplicativo móvel quando o push for executado na próxima vez em um estado conectado.

1. Edite App.xaml.cs no projeto compartilhado. Comente a inicialização do **MobileServiceClient** e adicione a seguinte linha, que usa uma URL de aplicativo móvel inválida:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celular no dispositivo ou usar o modo avião.
2. Pressione **F5** para compilar e executar o aplicativo. Observe que a sincronização falhou na atualização quando o aplicativo é iniciado.
3. Insira novos itens e observe que o push falha com um status de [CancelledByNetworkError] cada vez que você clica em **salvar**. No entanto, os novos itens de tarefas pendentes existem no repositório local até que possam ser enviados por push para o back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções, o aplicativo cliente se comporta como se ainda estiver conectado ao back-end do aplicativo móvel.
4. Feche o aplicativo e reinicie-o para verificar se os novos itens criados são persistidos no repositório local.
5. Adicional No Visual Studio, abra **Gerenciador de servidores**. Navegue até seu banco de dados no **Azure**-> bancos de dados**SQL**. Clique com o botão direito do mouse no banco de dados e selecione **abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar até a tabela do banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dado back-end não foram alterados.
6. Adicional Use uma ferramenta REST como o Fiddler ou o postmaster para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar o back-end do aplicativo móvel
Nesta seção, você reconecta o aplicativo ao back-end do aplicativo móvel. Essas alterações simulam uma reconexão de rede no aplicativo.

Quando você executa o aplicativo pela primeira vez, o manipulador de eventos `OnNavigatedTo` chama `InitLocalStoreAsync`. Esse método, por sua vez, chama `SyncAsync` para sincronizar seu repositório local com o banco de dados back-end. O aplicativo tenta sincronizar na inicialização.

1. Abra App.xaml.cs no projeto compartilhado e remova a marca de comentário da inicialização anterior do `MobileServiceClient` para usar a URL correta do aplicativo móvel.
2. Pressione a tecla **F5** para recompilar e executar o aplicativo. O aplicativo sincroniza suas alterações locais com o back-end do aplicativo móvel do Azure usando operações de push e pull quando o manipulador de eventos `OnNavigatedTo` é executado.
3. Adicional Exiba os dados atualizados usando Pesquisador de Objetos do SQL Server ou uma ferramenta REST como o Fiddler. Observe que os dados foram sincronizados entre o banco de dado back-end do aplicativo móvel do Azure e o repositório local.
4. No aplicativo, clique na caixa de seleção ao lado de alguns itens para concluí-los no repositório local.

   chamadas `UpdateCheckedTodoItem` `SyncAsync` para sincronizar cada item concluído com o back-end do aplicativo móvel. `SyncAsync` chama push e pull. No entanto, **sempre que você executar um pull em uma tabela na qual o cliente fez alterações, um push será sempre executado automaticamente**. Esse comportamento garante que todas as tabelas no repositório local junto com as relações permaneçam consistentes. Esse comportamento pode resultar em um envio inesperado.  Para obter mais informações sobre esse comportamento, consulte [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="api-summary"></a>Resumo da API
Para dar suporte aos recursos offline dos serviços móveis, usamos a interface [IMobileServiceSyncTable] e inicializamos [MobileServiceClient. SyncContext][synccontext] com um banco de dados SQLite local. Quando offline, as operações CRUD normais para aplicativos móveis funcionam como se o aplicativo ainda estiver conectado enquanto as operações ocorrerem no repositório local. Os métodos a seguir são usados para sincronizar o repositório local com o servidor:

* **[PushAsync]** Como esse método é um membro de [IMobileServicesSyncContext], as alterações em todas as tabelas são enviadas por push para o back-end. Somente os registros com alterações locais são enviados ao servidor.
* **[PullAsync]** Um pull é iniciado a partir de um [IMobileServiceSyncTable]. Quando há alterações controladas na tabela, um push implícito é executado para garantir que todas as tabelas no repositório local junto com as relações permaneçam consistentes. O parâmetro *pushOtherTables* controla se outras tabelas no contexto são enviadas por push em um envio implícito. O parâmetro de *consulta* usa uma [IMobileServiceTableQuery @ no__t-2T >][IMobileServiceTableQuery] ou uma cadeia de caracteres de consulta OData para filtrar os dados retornados. O parâmetro *QueryId* é usado para definir a sincronização incremental. Para obter mais informações, consulte [sincronização de dados offline em aplicativos móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Seu aplicativo deve chamar esse método periodicamente para limpar dados obsoletos do repositório local. Use o parâmetro *Force* quando precisar limpar as alterações que ainda não foram sincronizadas.

Para obter mais informações sobre esses conceitos, consulte [sincronização de dados offline em aplicativos móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações
Os tópicos a seguir fornecem informações adicionais sobre o recurso de sincronização offline dos aplicativos móveis:

* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[criar um aplicativo do Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
