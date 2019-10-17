---
title: Habilitar sincronização offline para seu aplicativo móvel do Azure (Cordova) | Microsoft Docs
description: Saiba como usar o aplicativo móvel do serviço de aplicativo para armazenar em cache e sincronizar dados offline em seu aplicativo Cordova
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: c5fcc98bf3da79d1978da6e67535a5a93a8fe321
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388854"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Habilitar sincronização offline para seu aplicativo móvel do Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial apresenta o recurso de sincronização offline dos aplicativos móveis do Azure para Cordova. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel @ no__t-0viewing, adicionando ou modificando dados @ no__t-1even quando não há conexão de rede. As alterações são armazenadas em um banco de dados local.  Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Este tutorial se baseia na solução de início rápido do Cordova para aplicativos móveis que você cria ao concluir o tutorial [Início rápido de Apache Cordova]. Neste tutorial, você atualiza a solução de início rápido para adicionar recursos offline de aplicativos móveis do Azure.  Também destacamos o código específico offline no aplicativo.

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de Dados Offline em Aplicações Móveis do Azure]. Para obter detalhes sobre o uso da API, consulte a [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicionar sincronização offline à solução de início rápido
O código de sincronização offline deve ser adicionado ao aplicativo. A sincronização offline requer o plug-in Cordova-SQLite-Storage, que é automaticamente adicionado ao seu aplicativo quando o plug-in de aplicativos móveis do Azure é incluído no projeto. O projeto de início rápido inclui esses dois plug-ins.

1. No Gerenciador de Soluções do Visual Studio, Abra index. js e substitua o código a seguir

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    com este código:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Em seguida, substitua o código a seguir:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    com este código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    As adições de código anteriores inicializam o repositório local e definem uma tabela local que corresponde aos valores de coluna usados no back-end do Azure. (Você não precisa incluir todos os valores de coluna neste código.)  O campo `version` é mantido pelo back-end móvel e é usado para resolução de conflitos.

    Você Obtém uma referência para o contexto de sincronização chamando **getSyncContext**. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar por push as alterações em todas as tabelas que um aplicativo cliente modificou quando `.push()` é chamado.

3. Atualize a URL do aplicativo para a URL do aplicativo móvel.

4. Em seguida, substitua este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    com este código:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    O código anterior Inicializa o contexto de sincronização e, em seguida, chama getsynctable (em vez de GetTable) para obter uma referência à tabela local.

    Esse código usa o banco de dados local para todas as operações de tabela de criação, leitura, atualização e exclusão (CRUD).

    Este exemplo executa o tratamento de erros simples em conflitos de sincronização. Um aplicativo real trataria os vários erros, como condições de rede, conflitos de servidor e outros. Para obter exemplos de código, consulte o [exemplo de sincronização offline].

5. Em seguida, adicione essa função para executar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Você decide quando enviar por push as alterações para o back-end do aplicativo móvel chamando **SyncContext. push ()** . Por exemplo, você poderia chamar **syncBackend** em um manipulador de eventos de botão vinculado a um botão de sincronização.

## <a name="offline-sync-considerations"></a>Considerações sobre sincronização offline

No exemplo, o método **Push** de **SyncContext** é chamado apenas na inicialização do aplicativo na função de retorno de chamada para logon.  Em um aplicativo do mundo real, você também poderia tornar essa funcionalidade de sincronização disparada manualmente ou quando o estado da rede for alterado.

Quando um pull é executado em uma tabela que tem atualizações locais pendentes controladas pelo contexto, essa operação de pull dispara automaticamente um envio por push. Ao atualizar, adicionar e concluir itens neste exemplo, você pode omitir a chamada **Push** explícita, pois ela pode ser redundante.

No código fornecido, todos os registros na tabela todoItem remota são consultados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta para **enviar por push**. Para obter mais informações, consulte a seção *sincronização incremental* em [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="optional-disable-authentication"></a>Adicional Desabilitar autenticação

Se você não quiser configurar a autenticação antes de testar a sincronização offline, comente a função de retorno de chamada para o logon, mas deixe o código dentro da função de retorno de chamada sem comentário.  Depois de comentar as linhas de logon, o código a seguir:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, o aplicativo é sincronizado com o back-end do Azure quando você executa o aplicativo.

## <a name="run-the-client-app"></a>Executar o aplicativo cliente
Com a sincronização offline agora habilitada, você pode executar o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados do repositório local. Posteriormente, simule um cenário offline e modifique os dados no repositório local enquanto o aplicativo estiver offline.

## <a name="optional-test-the-sync-behavior"></a>Adicional Testar o comportamento de sincronização
Nesta seção, você modifica o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Quando você adiciona ou altera itens de dados, essas alterações são mantidas no repositório local, mas não são sincronizadas com o armazenamento de dados de back-end até que a conexão seja restabelecida.

1. No Gerenciador de Soluções, abra o arquivo de projeto index. js e altere a URL do aplicativo para apontar para uma URL inválida, como o código a seguir:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Em index. html, atualize o elemento CSP `<meta>` com a mesma URL inválida.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Compile e execute o aplicativo cliente e observe que uma exceção é registrada no console do quando o aplicativo tenta sincronizar com o back-end após o logon. Todos os novos itens que você adicionar existirão somente no repositório local até que sejam enviados por push para o back-end móvel. O aplicativo cliente se comporta como se ele fosse conectado ao back-end.

4. Feche o aplicativo e reinicie-o para verificar se os novos itens criados são persistidos no repositório local.

5. Adicional Use o Visual Studio para exibir a tabela do banco de dados SQL do Azure para ver que os mesmos não foram alterados.

    No Visual Studio, abra **Gerenciador de servidores**. Navegue até seu banco de dados no **Azure**-> bancos de dados**SQL**. Clique com o botão direito do mouse no banco de dados e selecione **abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar até a tabela do banco de dados SQL e seu conteúdo.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Adicional Testar a reconexão com o back-end móvel

Nesta seção, você reconecta o aplicativo ao back-end móvel, que simula o aplicativo voltado para um estado online. Quando você faz logon, os dados são sincronizados com o back-end móvel.

1. Reabra o index. js e restaure a URL do aplicativo.
2. Reabra o index. html e corrija a URL do aplicativo no elemento CSP `<meta>`.
3. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após o logon. Verifique se nenhuma exceção é registrada no console de depuração.
4. Adicional Exiba os dados atualizados usando Pesquisador de Objetos do SQL Server ou uma ferramenta REST como o Fiddler. Note que os dados foram sincronizados entre o banco de dados back-end e o repositório local.

    Observe que os dados foram sincronizados entre o banco de dado e o repositório local e contêm os itens que você adicionou enquanto seu aplicativo foi desconectado.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Visual Studio Tools para Apache Cordova]

## <a name="next-steps"></a>Passos seguintes
* Examine recursos de sincronização offline mais avançados, como resolução de conflitos no [exemplo de sincronização offline]
* Examine a referência da API de sincronização offline na [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Início rápido de Apache Cordova]: app-service-mobile-cordova-get-started.md
[exemplo de sincronização offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
