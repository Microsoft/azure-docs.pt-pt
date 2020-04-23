---
title: Ativar sincronização offline para a sua aplicação UWP
description: Saiba como usar uma App Móvel Azure para cache e sincronizar dados offline na sua aplicação Universal Windows Platform (UWP).
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 236d4c199a13b02bcd82ae02657bbd35e45f729b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458822"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Ativar a sincronização offline da aplicação do Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra-lhe como adicionar suporte offline a uma aplicação universal Windows Platform (UWP) utilizando um backend da App Móvel Azure. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel - visualização, adição ou modificação de dados - mesmo quando não há ligação de rede. As mudanças são armazenadas numa base de dados local. Uma vez que o dispositivo esteja novamente on-line, estas alterações são sincronizadas com o backend remoto.

Neste tutorial, atualiza o projeto da aplicação UWP a partir do tutorial [Create a Windows] app para suportar as funcionalidades offline das Aplicações Móveis Azure. Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Offline Data Sync em Aplicações Móveis Azure].

## <a name="requirements"></a>Requisitos  
Este tutorial requer os seguintes pré-requisitos:

* Visual Studio 2013 a decorrer no Windows 8.1 ou mais tarde.
* A conclusão de [criar uma aplicação Do Windows]cria uma[aplicação para janelas.]
* [Loja Azure Mobile Services SQLite][sqlite store nuget]
* [SQLite para desenvolvimento da Plataforma Universal Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Atualize a aplicação do cliente para suportar funcionalidades offline
As funcionalidades offline da Azure Mobile App permitem interagir com uma base de dados local quando se está num cenário offline. Para utilizar estas funcionalidades na sua aplicação, ininicia uma interface [SyncContext.][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] A SQLite é utilizada como loja local no dispositivo.

1. Instale o [tempo de execução do SQLite para a Plataforma Universal do Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. No Visual Studio, abra o gestor de pacotes NuGet para o projeto de aplicação UWP que completou no tutorial de [aplicação Create a Windows.]
    Procure e instale o pacote **NuGet Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
3. No Solution Explorer, **referências** > de clique direito **Adicione referência...** > **Extensões** **universais do Windows,** > em seguida, ativar tanto **a SQLite para** a Plataforma Universal windows como para o Tempo de **Execução Visual C++ 2015 para aplicações da Plataforma Universal Windows.**

    ![Adicionar referência SQLite UWP][1]
4. Abra o ficheiro MainPage.xaml.cs `#define OFFLINE_SYNC_ENABLED` e descomente a definição.
5. No Visual Studio, prima a tecla **F5** para reconstruir e executar a aplicação de clientes. A aplicação funciona da mesma forma que antes de ativar a sincronização offline. No entanto, a base de dados local está agora povoada com dados que podem ser usados num cenário offline.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Atualize a aplicação para desligar do backend
Nesta secção, você quebra a ligação ao seu backend da Aplicação Móvel para simular uma situação offline. Ao adicionar itens de dados, o seu manipulador de exceção diz-lhe que a aplicação está em modo offline. Neste estado, novos itens adicionados na loja local e serão sincronizados com o backend da aplicação móvel quando o push for executado em um estado conectado.

1. Editar App.xaml.cs no projeto partilhado. Comentar a inicialização do **MobileServiceClient** e adicionar a seguinte linha, que utiliza um URL de aplicação móvel inválido:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Também pode demonstrar comportamento offline desativando redes wifi e celular estonteantes no dispositivo ou utilizar o modo avião.
2. Pressione **F5** para construir e executar a aplicação. Note que a sua sincronização falhou na atualização quando a aplicação foi lançada.
3. Introduza novos itens e note que o impulso falha com um estado [de Erro DeRede Cancelado cada] vez que clica em **Guardar**. No entanto, os novos itens todo existem na loja local até que possam ser empurrados para o backend da aplicação móvel.  Numa aplicação de produção, se suprimir estas exceções, a aplicação do cliente comporta-se como se ainda estivesse ligada ao backend da aplicação móvel.
4. Feche a aplicação e reinicie-a para verificar se os novos itens que criou são persuedia na loja local.
5. (Opcional) No Estúdio Visual, abra **o Server Explorer.** Navegue para a sua base de dados em Bases de Dados **Azure**->**SQL**. Clique na sua base de dados e selecione **Open no SQL Server Object Explorer**. Agora pode navegar na sua tabela de dados SQL e no seu conteúdo. Verifique se os dados na base de dados de backend não foram alterados.
6. (Opcional) Utilize uma ferramenta REST, como O Violinista ou o Carteiro, para consultar `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`o seu backend móvel, utilizando uma consulta GET no formulário .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Atualize a app para reconectar o seu backend da Aplicação Móvel
Nesta secção, volte a ligar a aplicação ao backend da aplicação móvel. Estas alterações simulam uma religação da rede na aplicação.

Quando executa a aplicação pela primeira vez, o manipulador de `OnNavigatedTo` eventos liga `InitLocalStoreAsync`. Este método, `SyncAsync` por sua vez, liga para sincronizar a sua loja local com a base de dados backend. A aplicação tenta sincronizar-se no arranque.

1. Abra App.xaml.cs no projeto partilhado e não comente a sua inicialização anterior para `MobileServiceClient` usar o URL correto da aplicação móvel.
2. Pressione a tecla **F5** para reconstruir e executar a aplicação. A aplicação sincroniza as suas alterações locais com o backend da App Móvel Azure utilizando operações de push e pull quando o manipulador de `OnNavigatedTo` eventos executa.
3. (Opcional) Veja os dados atualizados utilizando o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Note que os dados foram sincronizados entre a base de dados de backend da App Móvel Azure e a loja local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para os completar na loja local.

   `UpdateCheckedTodoItem`chamadas `SyncAsync` para sincronizar cada item concluído com o backend da Aplicação Móvel. `SyncAsync`chama empurrae puxar. No entanto, **sempre que executa um puxão contra uma mesa a que o cliente fez alterações, um empurrão é sempre executado automaticamente**. Este comportamento garante que todas as mesas da loja local, juntamente com as relações, se mantenham consistentes. Este comportamento pode resultar num impulso inesperado.  Para obter mais informações sobre este comportamento, consulte [Offline Data Sync em Aplicações Móveis Azure].

## <a name="api-summary"></a>Resumo da API
Para suportar as funcionalidades offline dos serviços móveis, usamos a interface [IMobileServiceSyncTable] e o [MobileServiceClient.SyncContext][synccontext] inicializado com uma base de dados Local SQLite. Quando offline, as operações normais de CRUD para Aplicações Móveis funcionam como se a aplicação ainda estivesse ligada enquanto as operações ocorrem contra a loja local. Os seguintes métodos são utilizados para sincronizar a loja local com o servidor:

* **[PushAsync]** Como este método é membro do [IMobileServicesSyncContext,]as mudanças em todas as tabelas são empurradas para o backend. Apenas os registos com alterações locais são enviados para o servidor.
* **[PullAsync]** Um pull é iniciado a partir de um [IMobileServiceSyncTable]. Quando há mudanças rastreadas na tabela, é feito um impulso implícito para garantir que todas as mesas da loja local, juntamente com as relações, se mantenham consistentes. O parâmetro *pushOtherTables* controla se outras tabelas no contexto são empurradas num impulso implícito. O parâmetro de *consulta* requer uma cadeia [\<iMobileServiceTableQuery T>][IMobileServiceTableQuery] ou oOData para filtrar os dados devolvidos. O parâmetro *consultaId* é usado para definir a sincronização incremental. Para mais informações, consulte [Offline Data Sync em Aplicações Móveis Azure](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgaAsync]** A sua aplicação deve chamar periodicamente este método para expurgar dados antigos da loja local. Utilize o parâmetro de *força* quando necessitar de purgar quaisquer alterações que ainda não tenham sido sincronizadas.

Para obter mais informações sobre estes conceitos, consulte [Offline Data Sync em Aplicações Móveis Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações
Os seguintes tópicos fornecem informações adicionais sobre o plano de sincronização offline de Aplicações Móveis:

* [Sincronização de Dados Offline nas Aplicações Móveis do Azure]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

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
[Sincronização de Dados Offline nas Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[criar uma aplicação para janelas]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[Contexto iMobileServicesSync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[Erro de rede cancelado]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgaAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
