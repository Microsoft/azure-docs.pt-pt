---
title: Ativar sincronização offline (Xamarin.Forms) / Microsoft Docs
description: Saiba como utilizar a App Service Mobile App para cache e sincronizar dados offline na sua aplicação Xamarin.Forms.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458856"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Ativar sincronização offline para a sua aplicação móvel Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial introduz a funcionalidade de sincronização offline de Aplicativos Móveis Azure para Xamarin.Forms. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel - visualização, adição ou modificação de dados - mesmo quando não há ligação de rede. As mudanças são armazenadas numa base de dados local. Uma vez que o dispositivo esteja novamente on-line, estas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução Xamarin.Forms quickstart para Aplicações Móveis que cria quando completa o tutorial [Create a Xamarin iOS app]. A solução de arranque rápido para Xamarin.Forms contém o código para suportar a sincronização offline, que só precisa de ser ativada. Neste tutorial, atualiza a solução quickstart para ligar as funcionalidades offline das Aplicações Móveis Azure. Destacamos também o código específico offline na aplicação. Se não utilizar a solução de arranque rápido descarregada, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure][1].

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Offline Data Sync em Aplicações Móveis Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Ativar a funcionalidade de sincronização offline na solução quickstart
O código de sincronização offline está incluído no projeto utilizando diretivas de pré-processador C#. Quando o símbolo **OFFLINE\_SYNC\_ENABLED** é definido, estes caminhos de código estão incluídos na construção. Para aplicações Windows, também deve instalar a plataforma SQLite.

1. No Estúdio Visual, clique na solução > **Gerir pacotes NuGet para solução...** e depois procure e instale o pacote **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet para todos os projetos da solução.
2. No Solution Explorer, abra o ficheiro TodoItemManager.cs do projeto com **a Portable** no nome, que é o projeto da Biblioteca de Classes Portáteis, e depois descodere a seguinte diretiva de pré-processador:

        #define OFFLINE_SYNC_ENABLED
3. (Opcional) Para suportar dispositivos Windows, instale um dos seguintes pacotes de tempo de execução SQLite:

   * **Windows 8.1 Tempo de execução:** Instale [o SQLite para o Windows 8.1][3].
   * **Windows Phone 8.1:** Instale [o SQLite para o Windows Phone 8.1][4].
   * **Plataforma Universal Windows** Instale [a SQLite para o Universal Windows Universal][5].

     Embora o quickstart não contenha um projeto Universal Windows, a plataforma Universal Windows é suportada com Formulários Xamarin.
4. (Opcional) Em cada projeto de aplicação do Windows, **referências** > de clique direito**Adicionar Referência...**, expandir a pasta **do Windows** > **Extensões**.
    Ative o **SQLite** adequado para Windows SDK juntamente com o Tempo de **Execução Visual C++ 2013 para Windows** SDK.
    Os nomes SQLite SDK variam ligeiramente com cada plataforma do Windows.

## <a name="review-the-client-sync-code"></a>Reveja o código de sincronização do cliente
Eis uma breve visão geral do que já está `#if OFFLINE_SYNC_ENABLED` incluído no código tutorial no interior das diretivas. A funcionalidade de sincronização offline está no ficheiro TodoItemManager.cs projeto no projeto Da Biblioteca de Classes Portáteis. Para uma visão geral conceptual da funcionalidade, consulte [offline Data Sync em Aplicações Móveis Azure][2].

* Antes de serem realizadas quaisquer operações de mesa, a loja local deve ser inicializada. A base de dados da loja local é inicializada no construtor classe **TodoItemManager** utilizando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código cria uma nova base de dados Local SQLite utilizando a classe **MobileServiceSQLiteStore.**

    O método **DefineTable** cria uma tabela na loja local que corresponde aos campos do tipo fornecido.  O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar um subconjunto de colunas.
* O campo **todoTable** em **TodoItemManager** é um tipo **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Esta classe utiliza a base de dados local para todas as operações de mesa de criação, leitura, atualização e exclusão (CRUD). Você decide quando essas alterações são empurradas para o backend da Aplicação Móvel, ligando **para PushAsync** no **IMobileServiceSyncContext**. O contexto de sincronização ajuda a preservar as relações de mesa, rastreando e empurrando mudanças em todas as tabelas que uma aplicação de cliente modificou quando o **PushAsync** é chamado.

    O seguinte método **SyncAsync** é chamado para sincronizar com o backend da Aplicação Móvel:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Esta amostra utiliza um simples manuseamento de erros com o manipulador de sincronização predefinido. Uma aplicação real lidaria com os vários erros, como condições de rede e conflitos de servidores, utilizando uma implementação **personalizada do IMobileServiceSyncHandler.**

## <a name="offline-sync-considerations"></a>Considerações de sincronização offline
Na amostra, o método **SyncAsync** só é solicitado no arranque e quando é solicitada uma sincronização.  Para iniciar uma sincronização numa aplicação Android ou iOS, puxe para baixo na lista de itens; para Windows, use o botão **Sync.** Numa aplicação no mundo real, também pode sabotar o sincronizador quando o estado da rede muda.

Quando um puxão é executado contra uma tabela que tem atualizações locais pendentes rastreadas pelo contexto, essa operação de puxar automaticamente dispara um impulso de contexto anterior. Ao refrescar, adicionar e completar itens nesta amostra, pode omitir a chamada explícita **PushAsync.**

No código fornecido, todos os registos na tabela TodoItem remoto são consultados, mas também é possível filtrar registos passando uma consulta e consulta a **PushAsync**. Para mais informações, consulte a secção *Incremental Sync* em Offline Data Sync [em Aplicações Móveis Azure][2].

## <a name="run-the-client-app"></a>Executar a aplicação de cliente
Com a sincronização offline agora ativada, execute a aplicação do cliente pelo menos uma vez em cada plataforma para povoar a base de dados da loja local. Mais tarde, simular um cenário offline e modificar os dados na loja local enquanto a aplicação está offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualizar o comportamento sincronizado da aplicação do cliente
Nesta secção, modifique o projeto do cliente para simular um cenário offline utilizando um URL de aplicação inválido para o seu backend. Em alternativa, pode desligar as ligações de rede movendo o seu dispositivo para "Modo Avião".  Quando adiciona ou altera itens de dados, estas alterações são realizadas na loja local, mas não sincronizadas com a loja de dados backend até que a ligação seja restabelecida.

1. No Solution Explorer, abra o ficheiro Constants.cs do projeto `ApplicationURL` do projeto **portátil** e altere o valor de apontar para um URL inválido:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Abra o ficheiro TodoItemManager.cs do projeto **portátil** e, em seguida, adicione uma **captura** para a classe base **Exception** à **tentativa... bloco de captura** em **SyncAsync**. Este bloco de **captura** escreve a mensagem de exceção para a consola, da seguinte forma:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Construa e execute a aplicação de cliente.  Adicione alguns itens novos. Note que uma exceção está registada na consola para cada tentativa de sincronização com o backend. Estes novos itens só existem na loja local até que possam ser empurrados para o backend móvel. A aplicação do cliente comporta-se como se estivesse ligada ao backend, suportando todas as operações de criação, leitura, atualização, exclusão (CRUD).
4. Feche a aplicação e reinicie-a para verificar se os novos itens que criou são persuedia na loja local.
5. (Opcional) Utilize o Visual Studio para visualizar a sua tabela de dados Azure SQL para ver se os dados na base de dados de backend não mudaram.

    No Estúdio Visual, abra **o Server Explorer.** Navegue para a sua base de dados em Bases de Dados **Azure**->**SQL**. Clique na sua base de dados e selecione **Open no SQL Server Object Explorer**. Agora pode navegar na sua tabela de dados SQL e no seu conteúdo.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualize a app do cliente para reconectar o seu backend móvel
Nesta secção, volte a ligar a app ao backend móvel, que simula o regresso da aplicação a um estado online. Quando executa o gesto de atualização, os dados são sincronizados com o seu backend móvel.

1. Reabra Constants.cs. Corrija `applicationURL` o ponto de vista para o URL correto.
2. Reconstruir e executar a aplicação de clientes. A aplicação tenta sincronizar com o backend da aplicação móvel após o lançamento. Verifique se não há exceções na consola de depuração.
3. (Opcional) Ver os dados atualizados utilizando o SQL Server Object Explorer ou uma ferramenta REST como O Violinista ou [o Carteiro][6]. Note que os dados foram sincronizados entre a base de dados de backend e a loja local.

    Note que os dados foram sincronizados entre a base de dados e a loja local e contém os itens que adicionou enquanto a sua aplicação foi desligada.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline nas Aplicações Móveis do Azure][2]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
