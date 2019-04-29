---
title: Permitir sincronização offline para a sua aplicação plataforma Universal do Windows (UWP) com as aplicações móveis | Documentos da Microsoft
description: Saiba como utilizar uma aplicação móvel do Azure para cache e sincronizar dados offline na sua aplicação plataforma Universal do Windows (UWP).
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128054"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Ativar a sincronização offline da aplicação do Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra-lhe como adicionar suporte offline para uma aplicação plataforma Universal do Windows (UWP) utilizando um back-end de aplicação móvel do Azure. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel, visualizar, adicionar ou modificar dados, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Depois do dispositivo estiver online novamente, essas alterações são sincronizadas com o back-end remoto.

Neste tutorial, atualizar o projeto de aplicação UWP partir do tutorial [criar uma aplicação do Windows] para suportar as funcionalidades offline de aplicações móveis do Azure. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="requirements"></a>Requisitos  
Este tutorial requer os seguintes pré-requisitos:

* O Visual Studio 2013 em execução no Windows 8.1 ou posterior.
* Conclusão da [criar uma aplicação do Windows][criar uma aplicação do windows].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite para o desenvolvimento de plataforma Universal do Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar as funcionalidades offline
Funcionalidades offline de aplicação móvel do Azure permitem-lhe interagir com bases de dados locais se estiver num cenário offline. Para usar esses recursos em seu aplicativo, inicializar uma [SyncContext] [ synccontext] para um armazenamento local. Em seguida, fazer referência a sua tabela através da [IMobileServiceSyncTable][IMobileServiceSyncTable] interface. SQLite é utilizado como armazenamento local no dispositivo.

1. Instalar o [SQLite tempo de execução para a plataforma Universal do Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. No Visual Studio, abra o Gestor de pacotes de NuGet para o projeto de aplicação UWP concluído no [criar uma aplicação do Windows] tutorial.
    Procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet.
3. No Solution Explorer, clique com botão direito **referências** > **Add Reference...** > **Windows universal** > **extensões**, em seguida, ative as **SQLite para a plataforma Universal do Windows** e **Visual C++ 2015 tempo de execução para Windows Universal Aplicações da plataforma**.

    ![Adicionar referência de SQLite UWP][1]
4. Abra o arquivo MainPage.xaml.cs e anule os comentários a `#define OFFLINE_SYNC_ENABLED` definição.
5. No Visual Studio, prima a **F5** chave para reconstruir e executar a aplicação de cliente. A aplicação funciona tal como fazia antes de ativar a sincronização offline. No entanto, a base de dados local agora é preenchido com dados que podem ser utilizados num cenário offline.

## <a name="update-sync"></a>Atualizar a aplicação ao desligar do back-end
Nesta seção, interromperá a ligação ao seu back-end de aplicação móvel para simular uma situação offline. Ao adicionar itens de dados, o seu manipulador de exceção indica que a aplicação está no modo offline. Neste estado, os novos itens adicionados no arquivo local e serão sincronizados com o back-end de aplicação móvel quando push em seguida é executado num estado ligado.

1. Edite App.xaml.cs no projeto partilhado. Comente a inicialização do **MobileServiceClient** e adicione a seguinte linha, que utiliza um URL de aplicação móvel inválido:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Também pode demonstrar o comportamento offline, desativando Wi-Fi e redes de celular no dispositivo ou utilizar o modo de avião.
2. Prima **F5** para compilar e executar a aplicação. Observe que a sincronização falha na atualização, quando a aplicação iniciada.
3. Introduza novos itens e tenha em atenção que o push falha com um [CancelledByNetworkError] estado de cada vez que clicar **guardar**. No entanto, os novos itens de afazeres existem no arquivo local até que estas podem ser enviados para o back-end de aplicação móvel.  Num aplicativo de produção, se suprimir essas exceções a aplicação de cliente se comporta como se ele ainda está ligado para o back-end de aplicação móvel.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou são persistidos no arquivo local.
5. (Opcional) No Visual Studio, abra **Explorador de servidores**. Navegue até à sua base de dados **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **aberto no SQL Server Object Explorer**. Agora pode navegar para a sua tabela de base de dados SQL e o respetivo conteúdo. Certifique-se de que os dados na base de dados back-end não mudou.
6. (Opcional) Utilize uma ferramenta REST, como o Fiddler ou o Postman para consultar o back-end móvel, usando uma consulta GET na forma `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação ligar novamente o seu back-end de aplicação móvel
Nesta secção, voltar a ligar a aplicação para o back-end de aplicação móvel. Estas alterações simular um restabelecimento de ligação de rede na aplicação.

Quando o aplicativo é executado primeiro o `OnNavigatedTo` chamadas de manipulador de eventos `InitLocalStoreAsync`. Por sua vez chama esse método `SyncAsync` para sincronizar o seu armazenamento local com a base de dados de back-end. A aplicação tenta fazer sincronização na inicialização.

1. Abra o App.xaml.cs no projeto partilhado e anule os comentários de sua inicialização anterior do `MobileServiceClient` para utilizar o URL da aplicação móvel a correta.
2. Prima a **F5** chave para reconstruir e executar a aplicação. A aplicação sincroniza as alterações de locais com o back-end de aplicação móvel do Azure a utilizar operações de push e pull quando a `OnNavigatedTo` executa o manipulador de eventos.
3. (Opcional) Ver os dados atualizados com o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Repare que os dados foram sincronizado entre a base de dados de back-end de aplicação móvel do Azure e o armazenamento local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para concluí-las no arquivo local.

   `UpdateCheckedTodoItem` chamadas `SyncAsync` ao item de sincronização cada foi concluída com o back-end de aplicação móvel. `SyncAsync` chama push e pull. No entanto, **sempre que executar uma solicitação em relação a uma tabela que o cliente fez alterações, um push é sempre executado automaticamente**. Esse comportamento garante que todas as tabelas no arquivo local, juntamente com as relações permanecerem consistentes. Esse comportamento pode resultar num push inesperado.  Para obter mais informações sobre este comportamento, consulte [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="api-summary"></a>Resumo de API
Para suportar as funcionalidades offline dos serviços móveis, usamos o [IMobileServiceSyncTable] interface e inicializado [MobileServiceClient.SyncContext] [ synccontext] com um base de dados SQLite local. Quando estiver offline, as operações de CRUD normais para Mobile Apps funcionam como se a aplicação ainda está ligada, enquanto as operações ocorrem em relação ao armazenamento local. Os métodos seguintes são utilizados para sincronizar o arquivo local com o servidor:

* **[PushAsync]**  porque este método é um membro de [IMobileServicesSyncContext], alterações em todas as tabelas são enviados por push para o back-end. Apenas os registos com alterações de locais são enviados para o servidor.
* **[PullAsync]**  uma solicitação é iniciada a partir de um [IMobileServiceSyncTable]. Quando existirem alterações registadas na tabela, um push implícita é executada para se certificar de que todas as tabelas no arquivo local, juntamente com as relações permanecerem consistentes. O *pushOtherTables* se outras tabelas no contexto são enviados por push num push implícita de controles de parâmetro. O *consulta* parâmetro assume um [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] ou cadeia de caracteres de consulta de OData para filtrar os dados retornados. O *queryId* parâmetro é utilizado para definir a sincronização incremental. Para obter mais informações, consulte [sincronização de dados Offline em aplicações móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  a aplicação deve chamar periodicamente este método para remover dados obsoletos do arquivo local. Utilize o *forçar* parâmetro quando tiver de remover todas as alterações que ainda não foram sincronizadas.

Para obter mais informações sobre estes conceitos, veja [sincronização de dados Offline em aplicações móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações
Os tópicos seguintes fornecem mais informações gerais sobre a funcionalidade de sincronização offline de aplicações móveis:

* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Aplicações móveis do Azure .NET SDK FOQUEI][8]

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
[criar uma aplicação do Windows]: app-service-mobile-windows-store-dotnet-get-started.md
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
