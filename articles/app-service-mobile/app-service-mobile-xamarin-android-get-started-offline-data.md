---
title: Permitir sincronização offline para a sua aplicação de Mobile do Azure (Xamarin Android)
description: Saiba como utilizar a aplicação serviço de aplicações móveis para a cache e sincronizar dados offline na sua aplicação Xamarin Android
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 7e951b9f2c2fda3c63f154b5b144addcbf65aacf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530336"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel xamarin. Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral

Este tutorial apresenta a funcionalidade de sincronização offline de aplicações móveis do Azure para xamarin. Android. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel, visualizar, adicionar ou modificar dados, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local.
Depois do dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Neste tutorial, atualizar o projeto de cliente a partir do tutorial [criar uma aplicação Xamarin Android] para suportar as funcionalidades offline de aplicações móveis do Azure. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar as funcionalidades offline

Funcionalidades offline de aplicação móvel do Azure permitem-lhe interagir com bases de dados locais se estiver num cenário offline. Para usar esses recursos em seu aplicativo, inicializar uma [SyncContext] para um armazenamento local. Em seguida, fazer referência a sua tabela através da [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) interface. SQLite é utilizado como armazenamento local no dispositivo.

1. No Visual Studio, abra o Gestor de pacotes de NuGet no projeto que concluiu no [criar uma aplicação Xamarin Android] tutorial.  Procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet.
2. Abra o ficheiro ToDoActivity.cs e anule os comentários a `#define OFFLINE_SYNC_ENABLED` definição.
3. No Visual Studio, prima a **F5** chave para reconstruir e executar a aplicação de cliente. A aplicação funciona tal como fazia antes de ativar a sincronização offline. No entanto, a base de dados local agora é preenchido com dados que podem ser utilizados num cenário offline.

## <a name="update-sync"></a>Atualizar a aplicação ao desligar do back-end

Nesta seção, interromperá a ligação ao seu back-end de aplicação móvel para simular uma situação offline. Ao adicionar itens de dados, o seu manipulador de exceção indica que a aplicação está no modo offline. Neste estado, os novos itens adicionados no arquivo local e são sincronizados para o back-end de aplicação móvel quando um push é executado num estado ligado.

1. Edite ToDoActivity.cs no projeto partilhado. Alteração da **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstrar o comportamento offline desabilitando Wi-Fi e redes de celular no dispositivo ou utilizar o modo de avião.
2. Prima **F5** para compilar e executar a aplicação. Observe que a sincronização falha na atualização, quando a aplicação iniciada.
3. Introduza novos itens e tenha em atenção que push falha com um Estado de [CancelledByNetworkError], cada vez que clicar **guardar**. No entanto, os novos itens de afazeres existem no arquivo local até que estas podem ser enviados para o back-end de aplicação móvel.  Num aplicativo de produção, se suprimir essas exceções a aplicação de cliente se comporta como se ele ainda está ligado para o back-end de aplicação móvel.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou são persistidos no arquivo local.
5. (Opcional) No Visual Studio, abra **Explorador de servidores**. Navegue até à sua base de dados **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **aberto no SQL Server Object Explorer**. Agora pode navegar para a sua tabela de base de dados SQL e o respetivo conteúdo. Certifique-se de que os dados na base de dados back-end não mudou.
6. (Opcional) Utilize uma ferramenta REST, como o Fiddler ou o Postman para consultar o back-end móvel, usando uma consulta GET na forma `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação ligar novamente o seu back-end de aplicação móvel

Nesta secção, voltar a ligar a aplicação para o back-end de aplicação móvel. Quando o aplicativo é executado primeiro o `OnCreate` chamadas de manipulador de eventos `OnRefreshItemsSelected`. Esse método chama `SyncAsync` para sincronizar o seu armazenamento local com a base de dados de back-end.

1. Abrir ToDoActivity.cs no projeto partilhado e reverter a alteração do **applicationURL** propriedade.
2. Prima a **F5** chave para reconstruir e executar a aplicação. A aplicação sincroniza as alterações de locais com o back-end de aplicação móvel do Azure a utilizar operações de push e pull quando a `OnRefreshItemsSelected` método é executado.
3. (Opcional) Ver os dados atualizados com o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Repare que os dados foram sincronizado entre a base de dados de back-end de aplicação móvel do Azure e o armazenamento local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para concluí-las no arquivo local.

   `CheckItem` chamadas `SyncAsync` ao item de sincronização cada foi concluída com o back-end de aplicação móvel. `SyncAsync` chama push e pull. **Sempre que executar uma solicitação em relação a uma tabela que o cliente fez alterações, um push é sempre executado automaticamente**. Isto garante que todas as tabelas no arquivo local, juntamente com as relações permanecerem consistentes. Esse comportamento pode resultar num push inesperado. Para obter mais informações sobre este comportamento, consulte [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização do cliente

O projeto de cliente do Xamarin que transferiu quando concluiu o tutorial [criar uma aplicação Xamarin Android] já contém o código de suporte a sincronização offline, usando um banco de dados local do SQLite. Eis uma breve descrição geral do que já está incluído no código do tutorial. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline em aplicações móveis do Azure].

* Antes de quaisquer operações de tabela podem ser realizadas, o arquivo local tem de ser inicializado. A base de dados do arquivo local é inicializado quando `ToDoActivity.OnCreate()` executa `ToDoActivity.InitLocalStoreAsync()`. Este método cria um local SQLite base de dados com o `MobileServiceSQLiteStore` classe fornecida pelo SDK de cliente de Mobile Apps do Azure.

    O `DefineTable` método cria uma tabela no arquivo local que corresponde aos campos no tipo fornecido, `ToDoItem` neste caso. O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar apenas um subconjunto de colunas.

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
* O `toDoTable` membro de `ToDoActivity` é da `IMobileServiceSyncTable` em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona a todos os criarem, ler, atualizar e eliminar (CRUD) de operações de tabela na base de dados do arquivo local.

    Decidir quando as alterações são enviados por push para o back-end de aplicação móvel do Azure ao chamar `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar as alterações em todas as tabelas de uma aplicação de cliente modificou quando `PushAsync` é chamado.

    As chamadas de código fornecido `ToDoActivity.SyncAsync()` para sincronização sempre que a lista de todoitem é atualizada ou um todoitem é adicionado ou foi concluído. As sincronizações de código após cada mudança de local.

    No código fornecido, todos os registos numa remoto `TodoItem` tabela são consultados, mas também é possível filtrar registos ao transmitir um id de consulta e a consulta para `PushAsync`. Para obter mais informações, consulte a secção *Sincronização Incremental* na [sincronização de dados Offline em aplicações móveis do Azure].

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
* [Aplicações móveis do Azure .NET SDK FOQUEI][8]

<!-- URLs. -->
[Criar uma aplicação Xamarin Android]: ./app-service-mobile-xamarin-android-get-started.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Criar uma aplicação Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
