---
title: Permitir sincronização offline para a sua aplicação de Mobile do Azure (Xamarin iOS)
description: Saiba como utilizar a aplicação serviço de aplicações móveis para a cache e sincronizar dados offline na sua aplicação Xamarin iOS
documentationcenter: xamarin
author: elamalani
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b87a1d86370e3abdb200b691d5216b1262512b3e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440050"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Permitir a sincronização offline para a sua aplicação móvel Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Descrição geral
Este tutorial apresenta a funcionalidade de sincronização offline de aplicações móveis do Azure para xamarin. IOS. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel, visualizar, adicionar ou modificar dados, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Depois do dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Neste tutorial, atualizar o projeto de aplicação xamarin. IOS partir [criar uma aplicação Xamarin iOS] para suportar as funcionalidades offline de aplicações móveis do Azure. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar as funcionalidades offline
Funcionalidades offline de aplicação móvel do Azure permitem-lhe interagir com bases de dados locais se estiver num cenário offline. Para usar esses recursos na sua aplicação, inicializar um [SyncContext] para um armazenamento local. Referência de tabela por meio da interface de [IMobileServiceSyncTable]. SQLite é utilizado como armazenamento local no dispositivo.

1. Abra o Gestor de pacotes de NuGet no projeto que concluiu no [criar uma aplicação Xamarin iOS] tutorial, em seguida, procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet.
2. Abra o ficheiro QSTodoService.cs e anule os comentários a `#define OFFLINE_SYNC_ENABLED` definição.
3. Recompile e execute a aplicação de cliente. A aplicação funciona tal como fazia antes de ativar a sincronização offline. No entanto, a base de dados local agora é preenchido com dados que podem ser utilizados num cenário offline.

## <a name="update-sync"></a>Atualizar a aplicação ao desligar do back-end
Nesta seção, interromperá a ligação ao seu back-end de aplicação móvel para simular uma situação offline. Ao adicionar itens de dados, o seu manipulador de exceção indica que a aplicação está no modo offline. Neste estado, os novos itens adicionados no arquivo local e serão sincronizados com o back-end de aplicação móvel quando push em seguida é executado num estado ligado.

1. Edite QSToDoService.cs no projeto partilhado. Alteração da **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstrar o comportamento offline desabilitando Wi-Fi e redes de celular no dispositivo ou utilizar o modo de avião.
2. Compile e execute a aplicação. Observe que a sincronização falha na atualização, quando a aplicação iniciada.
3. Introduza novos itens e tenha em atenção que push falha com um Estado de [CancelledByNetworkError], cada vez que clicar **guardar**. No entanto, os novos itens de afazeres existem no arquivo local até que estas podem ser enviados para o back-end de aplicação móvel.  Num aplicativo de produção, se suprimir essas exceções a aplicação de cliente se comporta como se ele ainda está ligado para o back-end de aplicação móvel.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou são persistidos no arquivo local.
5. (Opcional) Se tiver o Visual Studio instalado num PC, abra **Explorador de servidores**. Navegue até à sua base de dados **Azure**-> **bases de dados SQL**. A base de dados com o botão direito e selecione **aberto no SQL Server Object Explorer**. Agora pode navegar para a sua tabela de base de dados SQL e o respetivo conteúdo. Certifique-se de que os dados na base de dados back-end não mudou.
6. (Opcional) Utilize uma ferramenta REST, como o Fiddler ou o Postman para consultar o back-end móvel, usando uma consulta GET na forma `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação ligar novamente o seu back-end de aplicação móvel
Nesta secção, voltar a ligar a aplicação para o back-end de aplicação móvel. Isso simula a mover-se de um estado offline para um estado online com o back-end de aplicação móvel de aplicação.   Se simulated breakage a rede, desligando a conectividade de rede, sem alterações de código são necessários.
Ative a rede novamente.  Quando o aplicativo é executado pela primeira vez o `RefreshDataAsync` método é chamado. Isso por sua vez chama `SyncAsync` para sincronizar o seu armazenamento local com a base de dados de back-end.

1. Abrir QSToDoService.cs no projeto partilhado e reverter a alteração do **applicationURL** propriedade.
2. Recompile e execute a aplicação. A aplicação sincroniza as alterações de locais com o back-end de aplicação móvel do Azure a utilizar operações de push e pull quando a `OnRefreshItemsSelected` método é executado.
3. (Opcional) Ver os dados atualizados com o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Repare que os dados foram sincronizado entre a base de dados de back-end de aplicação móvel do Azure e o armazenamento local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para concluí-las no arquivo local.

   `CompleteItemAsync` chamadas `SyncAsync` ao item de sincronização cada foi concluída com o back-end de aplicação móvel. `SyncAsync` chama push e pull.
   **Sempre que executar uma solicitação em relação a uma tabela que o cliente fez alterações, um push no contexto de sincronização de cliente é sempre executado primeiro automaticamente**. O push implícita garante que todas as tabelas no arquivo local, juntamente com as relações permanecerem consistentes. Para obter mais informações sobre este comportamento, consulte [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização do cliente
O projeto de cliente do Xamarin que transferiu quando concluiu o tutorial [criar uma aplicação Xamarin iOS] já contém o código de suporte a sincronização offline, usando um banco de dados local do SQLite. Eis uma breve descrição geral do que já está incluído no código do tutorial. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline em aplicações móveis do Azure].

* Antes de quaisquer operações de tabela podem ser realizadas, o arquivo local tem de ser inicializado. A base de dados do arquivo local é inicializado quando `QSTodoListViewController.ViewDidLoad()` executa `QSTodoService.InitializeStoreAsync()`. Este método cria um novo local SQLite da base de dados utilizando o `MobileServiceSQLiteStore` classe fornecida pelo SDK de cliente de aplicação móvel do Azure.

    O `DefineTable` método cria uma tabela no arquivo local que corresponde aos campos no tipo fornecido, `ToDoItem` neste caso. O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar apenas um subconjunto de colunas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* O `todoTable` membro de `QSTodoService` é da `IMobileServiceSyncTable` em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona a todos os criarem, ler, atualizar e eliminar (CRUD) de operações de tabela na base de dados do arquivo local.

    Decidir quando essas alterações são enviados por push para o back-end de aplicação móvel do Azure ao chamar `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar as alterações em todas as tabelas de uma aplicação de cliente modificou quando `PushAsync` é chamado.

    As chamadas de código fornecido `QSTodoService.SyncAsync()` para sincronização sempre que a lista de todoitem é atualizada ou um todoitem é adicionado ou foi concluído. As sincronizações de aplicação depois de cada mudança de local. Se uma solicitação for executada com base numa tabela que tem atualizações locais pendentes controlados pelo contexto, essa operação de solicitação automaticamente acionará um push de contexto em primeiro lugar.

    No código fornecido, todos os registos numa remoto `TodoItem` tabela são consultados, mas também é possível filtrar registos ao transmitir um id de consulta e a consulta para `PushAsync`. Para obter mais informações, consulte a secção *Sincronização Incremental* na [sincronização de dados Offline em aplicações móveis do Azure].

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
* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Aplicações móveis do Azure .NET SDK FOQUEI][8]

<!-- Images -->

<!-- URLs. -->
[Criar uma aplicação Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
