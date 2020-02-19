---
title: Ativar sincronização offline (Cordova)
description: Saiba como utilizar a App Service Mobile App para cache e sincronizar dados offline na sua aplicação Cordova.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459423"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Ativar sincronização offline para a sua aplicação móvel Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial introduz a funcionalidade de sincronização offline de Aplicações Móveis Azure para cordova. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel&mdash;visualização, adição ou modificação de dados&mdash;mesmo quando não há ligação de rede. As mudanças são armazenadas numa base de dados local.  Uma vez que o dispositivo esteja novamente on-line, estas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução de quickstart da Cordova para Aplicações Móveis que cria quando completa o início rápido do tutorial [Apache Cordova arranque rápido] Neste tutorial, atualiza a solução quickstart para adicionar funcionalidades offline de Aplicações Móveis Azure.  Destacamos também o código específico offline na aplicação.

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Sincronização de Dados Offline em Aplicações Móveis do Azure]. Para mais detalhes sobre o uso da API, consulte a documentação da [API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicione sincronização offline à solução quickstart
O código de sincronização offline deve ser adicionado à aplicação. A sincronização offline requer o plugin de armazenamento cordova-sqlite, que é automaticamente adicionado à sua aplicação quando o plugin de Aplicações Móveis Azure está incluído no projeto. O projeto Quickstart inclui ambos estes plugins.

1. No Visual Studio's Solution Explorer, abra o index.js e substitua o seguinte código

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    com este código:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Em seguida, substitua o seguinte código:

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

    As adições de código anteriores inicializam a loja local e definem uma tabela local que corresponda aos valores da coluna utilizados na sua extremidade Azure. (Não precisa de incluir todos os valores da coluna neste código.)  O campo `version` é mantido pelo backend móvel e é utilizado para resolução de conflitos.

    Obtém uma referência ao contexto de sincronização chamando **getSyncContext**. O contexto de sincronização ajuda a preservar as relações de mesa, rastreando e empurrando mudanças em todas as tabelas que uma aplicação de cliente modificou quando `.push()` é chamada.

3. Atualize o URL da aplicação para o URL da aplicação da Aplicação Móvel.

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

    O código anterior inicializa o contexto de sincronização e, em seguida, chama getSyncTable (em vez de getTable) para obter uma referência à tabela local.

    Este código utiliza a base de dados local para todas as operações de tabela, leitura, atualização e exclusão (CRUD).

    Esta amostra executa um simples manuseamento de erros em conflitos de sincronização. Uma aplicação real lidaria com os vários erros como condições de rede, conflitos de servidores, entre outros. Para exemplos de código, consulte a [amostra de sincronização offline].

5. Em seguida, adicione esta função para executar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Você decide quando empurrar alterações para o backend da Aplicação Móvel, chamando **syncContext.push()** . Por exemplo, pode ligar para **o syncBackend** num manipulador de eventos de botão ligado a um botão de sincronização.

## <a name="offline-sync-considerations"></a>Considerações de sincronização offline

Na amostra, **o** método push do **syncContext** é apenas chamado para o arranque da aplicação na função de callback para login.  Numa aplicação no mundo real, também pode fazer com que esta funcionalidade de sincronização seja acionada manualmente ou quando o estado de rede muda.

Quando um puxão é executado contra uma tabela que tem atualizações locais pendentes rastreadas pelo contexto, essa operação de puxar automaticamente dispara um impulso. Ao refrescar, adicionar e completar itens nesta amostra, pode omitir a chamada de **pressão** explícita, uma vez que pode ser redundante.

No código fornecido, todos os registos na tabela todoitem remoto são consultados, mas também é possível filtrar os registos passando uma consulta e consulta para **empurrar**. Para mais informações, consulte a secção *Incremental Sync* em Offline Data Sync [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Desativar a autenticação

Se não quiser configurar a autenticação antes de testar a sincronização offline, comente a função de chamada para login, mas deixe o código dentro da função de chamada sem comentários.  Depois de comentar as linhas de login, o código segue:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, a aplicação sincroniza-se com o backend Azure quando executa a aplicação.

## <a name="run-the-client-app"></a>Executar a aplicação de cliente
Com a sincronização offline agora ativada, pode executar a aplicação do cliente pelo menos uma vez em cada plataforma para povoar a base de dados da loja local. Mais tarde, simular um cenário offline e modificar os dados na loja local enquanto a aplicação está offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Teste o comportamento de sincronização
Nesta secção, modifica o projeto do cliente para simular um cenário offline utilizando um URL de aplicação inválido para o seu backend. Quando adiciona ou altera itens de dados, estas alterações são realizadas na loja local, mas não são sincronizadas na loja de dados backend até que a ligação seja restabelecida.

1. No Solution Explorer, abra o ficheiro do projeto index.js e altere o URL da aplicação para apontar para um URL inválido, como o seguinte código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Em index.html, atualize o elemento `<meta>` CSP com o mesmo URL inválido.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Construa e execute a aplicação do cliente e note que uma exceção é registada na consola quando a aplicação tenta sincronizar com o backend após o login. Quaisquer novos itens que adicione só existem na loja local até serem empurrados para o backend móvel. A aplicação do cliente comporta-se como se estivesse ligada ao backend.

4. Feche a aplicação e reinicie-a para verificar se os novos itens que criou são persuedia na loja local.

5. (Opcional) Utilize o Visual Studio para visualizar a sua tabela de dados Azure SQL para ver se os dados na base de dados de backend não mudaram.

    No Estúdio Visual, abra **o Server Explorer.** Navegue para a sua base de dados em Bases de Dados **Azure**->**SQL**. Clique na sua base de dados e selecione **Open no SQL Server Object Explorer**. Agora pode navegar na sua tabela de dados SQL e no seu conteúdo.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Teste a reconexão ao seu backend móvel

Nesta secção, reconecta a app ao backend móvel, que simula o regresso da aplicação a um estado online. Quando faz login, os dados são sincronizados com o seu backend móvel.

1. Reabra o index.js e restaure o URL da aplicação.
2. Reabra o índice.html e corrija o URL de aplicação no elemento `<meta>` CSP.
3. Reconstruir e executar a aplicação de clientes. A aplicação tenta sincronizar com o backend da aplicação móvel após o login. Verifique se não há exceções na consola de depuração.
4. (Opcional) Veja os dados atualizados utilizando o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Note que os dados foram sincronizados entre a base de dados de backend e a loja local.

    Note que os dados foram sincronizados entre a base de dados e a loja local e contém os itens que adicionou enquanto a sua aplicação foi desligada.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Visual Studio Tools para Apache Cordova]

## <a name="next-steps"></a>Passos seguintes
* Reveja funcionalidades de sincronização offline mais avançadas, tais como resolução de conflitos na [amostra de sincronização offline]
* Reveja a referência a API de sincronização offline na [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova arranque rápido]: app-service-mobile-cordova-get-started.md
[amostra de sincronização offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
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
