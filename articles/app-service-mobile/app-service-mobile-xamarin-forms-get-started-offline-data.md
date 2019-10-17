---
title: Habilitar sincronização offline para seu aplicativo móvel do Azure (Xamarin. Forms) | Microsoft Docs
description: Saiba como usar o aplicativo móvel do serviço de aplicativo para armazenar em cache e sincronizar dados offline em seu aplicativo Xamarin. Forms
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 65bd619a95b102c4ad4be57f1661a89fbae43e22
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388430"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial apresenta o recurso de sincronização offline de aplicativos móveis do Azure para Xamarin. Forms. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Este tutorial se baseia na solução de início rápido do Xamarin. Forms para aplicativos móveis que você cria ao concluir o tutorial [criar um aplicativo Xamarin iOS]. A solução de início rápido do Xamarin. Forms contém o código para dar suporte à sincronização offline, que só precisa ser habilitada. Neste tutorial, você atualizará a solução de início rápido para ativar os recursos offline dos aplicativos móveis do Azure. Também destacamos o código específico offline no aplicativo. Se você não usar a solução de início rápido baixada, deverá adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure][1].

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [sincronização de dados offline em aplicativos móveis do Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Habilitar a funcionalidade de sincronização offline na solução de início rápido
O código de sincronização offline é incluído no projeto usando C# diretivas de pré-processador. Quando o símbolo **offline @ no__t-1SYNC @ no__t-2ENABLED** é definido, esses caminhos de código são incluídos na compilação. Para aplicativos do Windows, você também deve instalar a plataforma SQLite.

1. No Visual Studio, clique com o botão direito do mouse na solução > **gerenciar pacotes NuGet para solução...** , procure e instale o pacote NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** para todos os projetos na solução.
2. No Gerenciador de Soluções, abra o arquivo TodoItemManager.cs do projeto com **portátil** no nome, que é o projeto de biblioteca de classes portátil e remova a marca de comentário da seguinte diretiva de pré-processador:

        #define OFFLINE_SYNC_ENABLED
3. Adicional Para dar suporte a dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de execução de Windows 8.1:** Instale o [SQLite para Windows 8.1][3].
   * **Windows Phone 8,1:** Instale o [SQLite para Windows Phone 8,1][4].
   * **Plataforma universal do Windows** Instale [o SQLite para o Universal universal do Windows][5].

     Embora o guia de início rápido não contenha um projeto universal do Windows, há suporte para a plataforma universal do Windows com o Xamarin Forms.
4. Adicional Em cada projeto de aplicativo do Windows, clique com o botão direito do mouse em **referências** > **Adicionar referência...** , expanda a pasta do **Windows** > **extensões**.
    Habilite o **SQLite apropriado para** o SDK do Windows juntamente com o **tempo de execução do Visual C++ 2013 para o SDK do Windows** .
    Os nomes do SDK do SQLite variam um pouco com cada plataforma Windows.

## <a name="review-the-client-sync-code"></a>Examinar o código de sincronização do cliente
Aqui está uma breve visão geral do que já está incluído no código do tutorial dentro das diretivas `#if OFFLINE_SYNC_ENABLED`. A funcionalidade de sincronização offline está no arquivo de projeto TodoItemManager.cs no projeto de biblioteca de classes portátil. Para obter uma visão geral conceitual do recurso, consulte [sincronização de dados offline em aplicativos móveis do Azure][2].

* Antes que qualquer operação de tabela possa ser executada, o repositório local deve ser inicializado. O banco de dados de armazenamento local é inicializado no construtor da classe **TodoItemManager** usando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Esse código cria um novo banco de dados SQLite local usando a classe **MobileServiceSQLiteStore** .

    O método **definotable** cria uma tabela no repositório local que corresponde aos campos no tipo fornecido.  O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar um subconjunto de colunas.
* O campo **todotable** em **TodoItemManager** é um tipo **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Essa classe usa o banco de dados local para todas as operações de tabela CRUD (criar, ler, atualizar e excluir). Você decide quando essas alterações são enviadas para o back-end do aplicativo móvel chamando **PushAsync** no **IMobileServiceSyncContext**. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar por push as alterações em todas as tabelas que um aplicativo cliente modificou quando **PushAsync** é chamado.

    O seguinte método **SyncAsync** é chamado para sincronizar com o back-end do aplicativo móvel:

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

    Este exemplo usa o tratamento de erros simples com o manipulador de sincronização padrão. Um aplicativo real trataria os vários erros, como condições de rede e conflitos de servidor, usando uma implementação de **IMobileServiceSyncHandler** personalizada.

## <a name="offline-sync-considerations"></a>Considerações sobre sincronização offline
No exemplo, o método **SyncAsync** é chamado somente na inicialização e quando uma sincronização é solicitada.  Para iniciar uma sincronização em um aplicativo Android ou iOS, puxe-a para baixo na lista de itens; para o Windows, use o botão **sincronizar** . Em um aplicativo do mundo real, você também pode fazer o gatilho de sincronização quando o estado da rede é alterado.

Quando um pull é executado em uma tabela que tem atualizações locais pendentes controladas pelo contexto, essa operação de pull dispara automaticamente um push de contexto anterior. Ao atualizar, adicionar e concluir itens neste exemplo, você pode omitir a chamada **PushAsync** explícita.

No código fornecido, todos os registros na tabela TodoItem remota são consultados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta para **PushAsync**. Para obter mais informações, consulte a seção *sincronização incremental* em [sincronização de dados offline em aplicativos móveis do Azure][2].

## <a name="run-the-client-app"></a>Executar o aplicativo cliente
Com a sincronização offline agora habilitada, execute o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados do repositório local. Posteriormente, simule um cenário offline e modifique os dados no repositório local enquanto o aplicativo estiver offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualizar o comportamento de sincronização do aplicativo cliente
Nesta seção, modifique o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Como alternativa, você pode desativar as conexões de rede movendo o dispositivo para "modo avião".  Quando você adiciona ou altera itens de dados, essas alterações são mantidas no repositório local, mas não sincronizadas com o armazenamento de dados de back-end até que a conexão seja restabelecida.

1. No Gerenciador de Soluções, abra o arquivo de projeto Constants.cs do projeto **portátil** e altere o valor de `ApplicationURL` para apontar para uma URL inválida:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Abra o arquivo TodoItemManager.cs do projeto **portátil** e, em seguida, adicione um **Catch** para a classe de **exceção** base ao **try... Catch** de bloco em **SyncAsync**. Esse bloco **Catch** grava a mensagem de exceção no console, da seguinte maneira:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Compile e execute o aplicativo cliente.  Adicione alguns novos itens. Observe que uma exceção é registrada no console do para cada tentativa de sincronização com o back-end. Esses novos itens existem somente no repositório local até que possam ser enviados por push para o back-end móvel. O aplicativo cliente se comporta como se ele estiver conectado ao back-end, dando suporte a todas as operações CRUD (criar, ler, atualizar, excluir).
4. Feche o aplicativo e reinicie-o para verificar se os novos itens criados são persistidos no repositório local.
5. Adicional Use o Visual Studio para exibir a tabela do banco de dados SQL do Azure para ver que os mesmos não foram alterados.

    No Visual Studio, abra **Gerenciador de servidores**. Navegue até seu banco de dados no **Azure**-> bancos de dados**SQL**. Clique com o botão direito do mouse no banco de dados e selecione **abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar até a tabela do banco de dados SQL e seu conteúdo.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualizar o aplicativo cliente para reconectar seu back-end móvel
Nesta seção, reconecte o aplicativo ao back-end móvel, que simula o aplicativo voltado para um estado online. Quando você executa o gesto de atualização, os dados são sincronizados com o back-end móvel.

1. Reabra o Constants.cs. Corrija o `applicationURL` para apontar para a URL correta.
2. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após a inicialização. Verifique se nenhuma exceção é registrada no console de depuração.
3. Adicional Exiba os dados atualizados usando Pesquisador de Objetos do SQL Server ou uma ferramenta REST como o Fiddler ou o [postmaster][6]. Note que os dados foram sincronizados entre o banco de dados back-end e o repositório local.

    Observe que os dados foram sincronizados entre o banco de dado e o repositório local e contêm os itens que você adicionou enquanto seu aplicativo foi desconectado.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure][2]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
