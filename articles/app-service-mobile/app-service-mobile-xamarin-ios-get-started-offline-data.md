---
title: Ativar sincronização offline (Xamarin iOS)
description: Saiba como utilizar a App Service Mobile App para cache e sincronizar dados offline na sua aplicação Xamarin iOS.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461305"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Ativar sincronização offline para a sua aplicação móvel Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial introduz a funcionalidade de sincronização offline de Aplicações Móveis Azure para Xamarin.iOS. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel - visualização, adição ou modificação de dados - mesmo quando não há ligação de rede. As mudanças são armazenadas numa base de dados local. Uma vez que o dispositivo esteja novamente on-line, estas alterações são sincronizadas com o serviço remoto.

Neste tutorial, atualize o projeto de aplicação Xamarin.iOS da [Create a Xamarin iOS app] para suportar as funcionalidades offline das Aplicações Móveis Azure. Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Offline Data Sync em Aplicações Móveis Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualize a aplicação do cliente para suportar funcionalidades offline
As funcionalidades offline da Azure Mobile App permitem interagir com uma base de dados local quando se está num cenário offline. Para utilizar estas funcionalidades na sua aplicação, inicie um [SyncContext] numa loja local. Consulte a sua tabela através da interface [IMobileServiceSyncTable]. A SQLite é utilizada como loja local no dispositivo.

1. Abra o gestor de pacotes NuGet no projeto que completou no tutorial de [aplicação Create a Xamarin iOS,] depois procure e instale o pacote **NuGet Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
2. Abra o ficheiro QSTodoService.cs `#define OFFLINE_SYNC_ENABLED` e descomente a definição.
3. Reconstruir e executar a aplicação de clientes. A aplicação funciona da mesma forma que antes de ativar a sincronização offline. No entanto, a base de dados local está agora povoada com dados que podem ser usados num cenário offline.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Atualize a aplicação para desligar do backend
Nesta secção, você quebra a ligação ao seu backend da Aplicação Móvel para simular uma situação offline. Ao adicionar itens de dados, o seu manipulador de exceção diz-lhe que a aplicação está em modo offline. Neste estado, novos itens adicionados na loja local e serão sincronizados com o backend da aplicação móvel quando o push for executado em um estado conectado.

1. Editar QSToDoService.cs no projeto partilhado. Alterar o URL de **aplicações** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstrar comportamento offline desativando redes wifi e celular estonteantes no dispositivo ou utilizando o modo avião.
2. Compile e execute a aplicação. Note que a sua sincronização falhou na atualização quando a aplicação foi lançada.
3. Introduza novos itens e note que o impulso falha com um estado [CancelledByNetworkError] sempre que clicar em **Guardar**. No entanto, os novos itens todo existem na loja local até que possam ser empurrados para o backend da aplicação móvel.  Numa aplicação de produção, se suprimir estas exceções, a aplicação do cliente comporta-se como se ainda estivesse ligada ao backend da aplicação móvel.
4. Feche a aplicação e reinicie-a para verificar se os novos itens que criou são persuedia na loja local.
5. (Opcional) Se tiver o Visual Studio instalado num PC, abra o **Server Explorer.** Navegue para a sua base de dados em Bases de Dados **Azure**-> **SQL**. Clique na sua base de dados e selecione **Open no SQL Server Object Explorer**. Agora pode navegar na sua tabela de dados SQL e no seu conteúdo. Verifique se os dados na base de dados de backend não foram alterados.
6. (Opcional) Utilize uma ferramenta REST, como O Violinista ou o Carteiro, para consultar `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`o seu backend móvel, utilizando uma consulta GET no formulário .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Atualize a app para reconectar o seu backend da Aplicação Móvel
Nesta secção, volte a ligar a app ao backend da aplicação móvel. Isto simula a aplicação movendo-se de um estado offline para um estado online com o backend da aplicação móvel.   Se simulasse a rutura da rede desligando a conectividade da rede, não são necessárias alterações de código.
Ligue a rede de novo.  Quando executa a aplicação pela primeira vez, o `RefreshDataAsync` método é chamado. Isto, por `SyncAsync` sua vez, liga para sincronizar a sua loja local com a base de dados backend.

1. Abra QSToDoService.cs no projeto partilhado e reverta a sua mudança da propriedade **applicationURL.**
2. Reconstruir e executar a aplicação. A aplicação sincroniza as suas alterações locais com o backend da App Móvel Azure utilizando operações de push e pull quando o `OnRefreshItemsSelected` método executa.
3. (Opcional) Veja os dados atualizados utilizando o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Note que os dados foram sincronizados entre a base de dados de backend da App Móvel Azure e a loja local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para os completar na loja local.

   `CompleteItemAsync`chamadas `SyncAsync` para sincronizar cada item concluído com o backend da Aplicação Móvel. `SyncAsync`chama empurrae puxar.
   **Sempre que executa um puxão contra uma mesa a que o cliente fez alterações, um impulso no contexto de sincronização do cliente é sempre executado automaticamente.** O impulso implícito garante que todas as mesas da loja local, juntamente com as relações, se mantenham consistentes. Para obter mais informações sobre este comportamento, consulte [Offline Data Sync em Aplicações Móveis Azure].

## <a name="review-the-client-sync-code"></a>Reveja o código de sincronização do cliente
O projeto de cliente Xamarin que descarregou quando completou o tutorial [Create a Xamarin iOS app] já contém código que suporta sincronização offline usando uma base de dados local Da QQLite. Aqui está uma breve visão geral do que já está incluído no código tutorial. Para uma visão geral conceptual da funcionalidade, consulte [offline Data Sync em Aplicações Móveis Azure].

* Antes de serem realizadas quaisquer operações de mesa, a loja local deve ser inicializada. A base de dados `QSTodoListViewController.ViewDidLoad()` da `QSTodoService.InitializeStoreAsync()`loja local é inicializada quando executa . Este método cria uma nova base `MobileServiceSQLiteStore` de dados Local SQLite utilizando a classe fornecida pelo cliente da App Móvel Azure SDK.

    O `DefineTable` método cria uma tabela na loja local que `ToDoItem` corresponde aos campos do tipo fornecido, neste caso. O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar apenas um subconjunto de colunas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* O `todoTable` membro `QSTodoService` é `IMobileServiceSyncTable` do tipo `IMobileServiceTable`em vez de . O IMobileServiceSyncTable direciona todas as operações de mesa para criar, ler, atualizar e eliminar (CRUD) as operações de mesa para a base de dados da loja local.

    Você decide quando essas alterações são empurradas para `IMobileServiceSyncContext.PushAsync()`o backend da App Móvel Azure, ligando . O contexto de sincronização ajuda a preservar as relações de mesa, `PushAsync` rastreando e empurrando mudanças em todas as tabelas que uma aplicação de cliente modificou quando é chamada.

    O código `QSTodoService.SyncAsync()` fornecido liga para sincronizar sempre que a lista de todooitem for atualizada ou um todoitem for adicionado ou concluído. A aplicação sincroniza após cada mudança local. Se um puxão for executado contra uma tabela que tem atualizações locais pendentes rastreadas pelo contexto, essa operação de puxar irá automaticamente desencadear um impulso de contexto primeiro.

    No código fornecido, todos os `TodoItem` registos na mesa remota são consultados, mas também é possível `PushAsync`filtrar os registos passando uma consulta e consulta para . Para mais informações, consulte a secção *Incremental Sync* em Offline Data Sync [em Aplicações Móveis Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline nas Aplicações Móveis do Azure]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Criar uma aplicação xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de Dados Offline nas Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
