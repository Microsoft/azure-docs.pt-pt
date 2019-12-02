---
title: Habilitar sincronização offline (Xamarin Android)
description: Saiba como usar o aplicativo móvel do serviço de aplicativo para armazenar em cache e sincronizar dados offline em seu aplicativo Xamarin Android.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c1119a803f160c1833cd87413263b868928b82c2
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668358"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Habilite a sincronização offline para seu aplicativo móvel Xamarin. Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Este tutorial apresenta o recurso de sincronização offline de aplicativos móveis do Azure para Xamarin. Android. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local.
Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Neste tutorial, você atualizará o projeto do cliente do tutorial [Criar um aplicativo Xamarin Android] para dar suporte aos recursos offline dos aplicativos móveis do Azure. Se você não usar o projeto baixado do servidor de início rápido, deverá adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar o aplicativo cliente para dar suporte a recursos offline

Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você inicializa um [SyncContext] para um repositório local. Em seguida, faça referência à tabela por meio da interface [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) . O SQLite é usado como o repositório local no dispositivo.

1. No Visual Studio, abra o Gerenciador de pacotes NuGet no projeto que você concluiu no tutorial [criar um aplicativo Xamarin Android] .  Procure e instale o pacote NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
2. Abra o arquivo ToDoActivity.cs e remova a marca de comentário da definição de `#define OFFLINE_SYNC_ENABLED`.
3. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo cliente. O aplicativo funciona da mesma forma que antes você habilitou a sincronização offline. No entanto, o banco de dados local agora é populado com aqueles que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo para desconectar-se do back-end

Nesta seção, você interrompe a conexão com o back-end do aplicativo móvel para simular uma situação offline. Quando você adiciona itens de dados, seu manipulador de exceção informa que o aplicativo está no modo offline. Nesse estado, novos itens adicionados no repositório local e são sincronizados com o back-end do aplicativo móvel quando um envio por push é executado em um estado conectado.

1. Edite ToDoActivity.cs no projeto compartilhado. Altere o **applicationURL** para apontar para uma URL inválida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celular no dispositivo ou usando o modo avião.
2. Pressione **F5** para compilar e executar o aplicativo. Observe que a sincronização falhou na atualização quando o aplicativo é iniciado.
3. Insira novos itens e observe que o push falha com um status [CancelledByNetworkError] toda vez que você clica em **salvar**. No entanto, os novos itens de tarefas pendentes existem no repositório local até que possam ser enviados por push para o back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções, o aplicativo cliente se comporta como se ainda estiver conectado ao back-end do aplicativo móvel.
4. Feche o aplicativo e reinicie-o para verificar se os novos itens criados são persistidos no repositório local.
5. Adicional No Visual Studio, abra **Gerenciador de servidores**. Navegue até seu banco de dados no **Azure**->bancos de dados **SQL**. Clique com o botão direito do mouse no banco de dados e selecione **abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar até a tabela do banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dado back-end não foram alterados.
6. Adicional Use uma ferramenta REST como o Fiddler ou o postmaster para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar o back-end do aplicativo móvel

Nesta seção, reconecte o aplicativo ao back-end do aplicativo móvel. Quando você executa o aplicativo pela primeira vez, o manipulador de eventos `OnCreate` chama `OnRefreshItemsSelected`. Esse método chama `SyncAsync` para sincronizar o armazenamento local com o banco de dados back-end.

1. Abra ToDoActivity.cs no projeto compartilhado e reverta a alteração da propriedade **applicationURL** .
2. Pressione a tecla **F5** para recompilar e executar o aplicativo. O aplicativo sincroniza as alterações locais com o back-end do aplicativo móvel do Azure usando as operações de push e pull quando o método de `OnRefreshItemsSelected` é executado.
3. Adicional Exiba os dados atualizados usando Pesquisador de Objetos do SQL Server ou uma ferramenta REST como o Fiddler. Observe que os dados foram sincronizados entre o banco de dado back-end do aplicativo móvel do Azure e o repositório local.
4. No aplicativo, clique na caixa de seleção ao lado de alguns itens para concluí-los no repositório local.

   `CheckItem` chama `SyncAsync` para sincronizar cada item concluído com o back-end do aplicativo móvel. `SyncAsync` chama push e pull. **Sempre que você executa um pull em uma tabela na qual o cliente fez alterações, um push é sempre executado automaticamente**. Isso garante que todas as tabelas no repositório local junto com as relações permaneçam consistentes. Esse comportamento pode resultar em um envio inesperado. Para obter mais informações sobre esse comportamento, consulte [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="review-the-client-sync-code"></a>Examinar o código de sincronização do cliente

O projeto do cliente Xamarin que você baixou quando concluiu o tutorial [criar um aplicativo Xamarin Android] já contém a sincronização offline de suporte a código usando um banco de dados SQLite local. Aqui está uma breve visão geral do que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, consulte [Sincronização de Dados Offline em Aplicações Móveis do Azure].

* Antes que qualquer operação de tabela possa ser executada, o repositório local deve ser inicializado. O banco de dados de armazenamento local é inicializado quando `ToDoActivity.OnCreate()` é executado `ToDoActivity.InitLocalStoreAsync()`. Esse método cria um banco de dados SQLite local usando a classe `MobileServiceSQLiteStore` fornecida pelo SDK do cliente dos aplicativos móveis do Azure.

    O método `DefineTable` cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, `ToDoItem` nesse caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* O membro `toDoTable` de `ToDoActivity` é do tipo `IMobileServiceSyncTable` em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona todas as operações de tabela CRUD (criar, ler, atualizar e excluir) para o banco de dados de repositório local.

    Você decide quando as alterações são enviadas para o back-end do aplicativo móvel do Azure chamando `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar por push as alterações em todas as tabelas que um aplicativo cliente modificou quando `PushAsync` é chamado.

    O código fornecido chama `ToDoActivity.SyncAsync()` para sincronizar sempre que a lista todoitem é atualizada ou um todoitem é adicionado ou concluído. O código é sincronizado após cada alteração local.

    No código fornecido, todos os registros na tabela de `TodoItem` remota são consultados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta para `PushAsync`. Para obter mais informações, consulte a seção *sincronização incremental* em [Sincronização de Dados Offline em Aplicações Móveis do Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos Adicionais

* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- URLs. -->
[Criar um aplicativo Xamarin Android]: ./app-service-mobile-xamarin-android-get-started.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
