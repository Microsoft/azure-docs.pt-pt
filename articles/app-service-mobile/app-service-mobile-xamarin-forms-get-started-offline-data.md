---
title: Permitir sincronização offline para a sua aplicação de Mobile do Azure (xamarin. Forms) | Documentos da Microsoft
description: Saiba como utilizar a aplicação serviço de aplicações móveis para a cache e sincronizar dados offline na sua aplicação xamarin. Forms
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
ms.openlocfilehash: 53f339d5450965c992f6528ff294e0d37ec2f7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446286"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Descrição geral
Este tutorial apresenta a funcionalidade de sincronização offline de aplicações móveis do Azure para xamarin. Forms. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel, visualizar, adicionar ou modificar dados, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Depois do dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se a ser a solução de início rápido do xamarin. Forms para Mobile Apps que criou quando concluir o tutorial [criar uma aplicação Xamarin iOS]. A solução de início rápido para xamarin. Forms contém o código para dar suporte a sincronização offline, que apenas precisa de ser ativada. Neste tutorial, atualize a solução de início rápido para ativar as funcionalidades offline de aplicações móveis do Azure. Também realçamos o código específico do offline na aplicação. Se não utilizar a solução de início rápido transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure][1].

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline em aplicações móveis do Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Ativar a funcionalidade de sincronização offline na solução de início rápido
O código de sincronização offline está incluído no projeto com o C# diretivas de pré-processamento. Quando o **OFFLINE\_SINCRONIZAÇÃO\_ATIVADO** símbolo é definido, estes caminhos de código estão incluídos na compilação. Para aplicações do Windows, deve instalar também a plataforma do SQLite.

1. No Visual Studio, clique com botão direito a solução > **gerir pacotes NuGet para solução de...** , em seguida, procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet para todos os projetos na solução.
2. No Explorador de soluções, abra o ficheiro TodoItemManager.cs do projeto com **portátil** o nome, o que é o projeto de biblioteca de classes portáteis, em seguida, anule os comentários a diretiva de pré-processador seguinte:

        #define OFFLINE_SYNC_ENABLED
3. (Opcional) Para suportar dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de execução do Windows 8.1:** Instale [SQLite para o Windows 8.1][3].
   * **Windows Phone 8.1:** Instale [SQLite para o Windows Phone 8.1][4].
   * **Plataforma Windows universal** instale [SQLite para o Universal do Windows Universal][5].

     Embora o guia de introdução não contém um projeto do Windows Universal, a plataforma Windows Universal é suportada com o Xamarin Forms.
4. (Opcional) Em cada projeto de aplicação do Windows, clique com botão direito **referências** > **Add Reference...** , expanda a **Windows** pasta > **extensões**.
    Ativar o adequado **SQLite para Windows** SDK juntamente com o **Visual C++ 2013 tempo de execução da Windows** SDK.
    Os nomes de SDK do SQLite são ligeiramente diferentes com cada plataforma do Windows.

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização do cliente
Eis uma breve descrição geral do que já está incluído no código do tutorial dentro do `#if OFFLINE_SYNC_ENABLED` diretivas. A funcionalidade de sincronização offline é no arquivo de projeto TodoItemManager.cs no projeto da biblioteca de classe portáteis. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline em aplicações móveis do Azure][2].

* Antes de quaisquer operações de tabela podem ser realizadas, o arquivo local tem de ser inicializado. A base de dados do arquivo local é inicializado no **TodoItemManager** construtor de classe usando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código cria um novo local SQLite da base de dados com o **MobileServiceSQLiteStore** classe.

    O **DefineTable** método cria uma tabela no arquivo local que corresponde aos campos no tipo fornecido.  O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar um subconjunto de colunas.
* O **todoTable** campo na **TodoItemManager** é um **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Este utiliza de classe base de dados local para todos os criar, ler, atualizar e eliminar (CRUD) de operações de tabela. Decidir quando essas alterações são enviados por push para o back-end de aplicação móvel chamando **PushAsync** sobre o **IMobileServiceSyncContext**. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar as alterações em todas as tabelas de uma aplicação de cliente modificou quando **PushAsync** é chamado.

    O seguinte procedimento **SyncAsync** método é chamado para sincronização com o back-end de aplicação móvel:

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

    Este exemplo utiliza com o manipulador de sincronização do padrão de processamento de erros simples. Um aplicativo real lidaria com vários erros, como condições de rede e conflitos de servidor utilizando um personalizado **IMobileServiceSyncHandler** implementação.

## <a name="offline-sync-considerations"></a>Considerações sobre a sincronização offline
No exemplo, o **SyncAsync** método apenas é chamado na inicialização e quando é solicitada uma sincronização.  Para iniciar uma sincronização num aplicativo Android ou iOS, obtido da lista de itens; para Windows, utilize o **sincronização** botão. Num aplicativo do mundo real, também poderia fazer o acionador de sincronização quando altera o estado de rede.

Quando uma solicitação é executada em relação a uma tabela com pendentes atualizações locais rastreados pelo contexto, que a solicitação de operação automaticamente acionadores um push de contexto anterior. Durante a atualização, adicionar e concluir os itens neste exemplo, pode omitir o explícita **PushAsync** chamar.

No código fornecido, todos os registos na tabela TodoItem remoto são consultados, mas também é possível filtrar registos ao transmitir um id de consulta e a consulta para **PushAsync**. Para obter mais informações, consulte a secção *Sincronização Incremental* na [sincronização de dados Offline em aplicações móveis do Azure][2].

## <a name="run-the-client-app"></a>Executar a aplicação de cliente
Com a sincronização offline, agora, execute a aplicação de cliente, pelo menos, uma vez em cada plataforma para preencher a base de dados do arquivo local. Mais tarde, simular um cenário offline e modificar os dados no arquivo local, enquanto a aplicação está offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualizar o comportamento de sincronização da aplicação de cliente
Nesta secção, modifique o projeto de cliente para simular o cenário offline utilizando um URL de aplicação inválido para o back-end. Em alternativa, pode desativar ligações de rede ao mover o seu dispositivo para o "Modo de avião".  Quando adicionar ou alterar itens de dados, estas alterações são mantidas no arquivo local, mas não sincronizadas com o arquivo de dados de back-end, até que a ligação seja restabelecida.

1. No Solution Explorer, abra o ficheiro de projeto de Constants.cs do **portátil** do projeto e altere o valor de `ApplicationURL` para apontar para um URL inválido:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Abra o ficheiro TodoItemManager.cs a **portátil** do projeto, em seguida, adicione um **capturar** para a base **exceção** classe para o **tente... catch**bloqueie no **SyncAsync**. Isso **capturar** bloco grava a mensagem de exceção na consola, da seguinte forma:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Crie e execute a aplicação de cliente.  Adicione alguns itens de novo. Tenha em atenção que uma exceção está registada na consola para cada tentativa de sincronização com o back-end. Estes novos itens existem apenas no arquivo local até que estas podem ser enviados para o back-end móvel. A aplicação de cliente se comporta como se ele está ligado ao back-end, suporte a que todos os criam, ler, atualizar, operações de eliminação (CRUD).
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou são persistidos no arquivo local.
5. (Opcional) Utilize o Visual Studio para ver a sua tabela de base de dados do Azure SQL para ver que os dados na base de dados back-end não mudou.

    No Visual Studio, abra **Explorador de servidores**. Navegue até à sua base de dados **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **aberto no SQL Server Object Explorer**. Agora pode navegar para a sua tabela de base de dados SQL e o respetivo conteúdo.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualizar a aplicação de cliente para ligar novamente o seu back-end móvel
Nesta secção, voltar a ligar a aplicação para o back-end móvel, o que simula a aplicação volte ao estado online. Ao efetuar o gesto de atualização, os dados são sincronizados com seu back-end móvel.

1. Reabra Constants.cs. Corrija o `applicationURL` para apontar para o URL correto.
2. Recompile e execute a aplicação de cliente. A aplicação tenta sincronizar com o back-end de aplicação móvel depois de iniciar. Certifique-se de que não existem exceções são registadas na consola de depuração.
3. (Opcional) Ver os dados atualizados com o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler ou [Postman][6]. Repare que os dados foram sincronizado entre a base de dados de back-end e o armazenamento local.

    Repare que os dados foram sincronizado entre a base de dados e o armazenamento local e contém os itens adicionados enquanto a aplicação foi desconectada.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure][2]
* [Aplicações móveis do Azure .NET SDK FOQUEI][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
