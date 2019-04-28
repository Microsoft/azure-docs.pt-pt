---
title: Permitir sincronização offline para a sua aplicação de Mobile do Azure (Cordova) | Documentos da Microsoft
description: Saiba como utilizar a aplicação serviço de aplicações móveis para a cache e sincronizar dados offline na sua aplicação Cordova
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110990"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel do Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Este tutorial apresenta a funcionalidade de sincronização offline de aplicações móveis do Azure para Cordova. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local.  Depois do dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução de início rápido do Cordova para Mobile Apps que criou quando concluir o tutorial [início rápido do Apache Cordova]. Neste tutorial, atualize a solução de início rápido para adicionar as funcionalidades offline de aplicações móveis do Azure.  Também realçamos o código específico do offline na aplicação.

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline em aplicações móveis do Azure]. Para obter detalhes de utilização da API, consulte a [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicionar sincronização offline para a solução de início rápido
Tem de adicionar o código de sincronização offline à aplicação. Sincronização offline requer o plug-in cordova sqlite-armazenamento, que é adicionado automaticamente à sua aplicação quando o plug-in de Mobile Apps do Azure está incluído no projeto. O projeto de início rápido inclui ambos esses plug-ins.

1. No Explorador de soluções do Visual Studio, abra Index e substitua o código a seguir

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

    As adições de código anterior inicializar o arquivo local e definir uma tabela local que corresponda os valores de coluna utilizados no seu Azure back-end. (Não precisa incluir todos os valores de coluna nesse código.)  O `version` campo é mantido pelo back-end móvel e é utilizado para a resolução de conflitos.

    Obtém uma referência ao contexto de sincronização ao chamar **getSyncContext**. O contexto de sincronização ajuda a preservar as relações de tabela ao controlar e enviar as alterações em todas as tabelas de uma aplicação de cliente modificou quando `.push()` é chamado.

3. Atualize o URL da aplicação para o URL da aplicação de aplicação móvel.

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

    Este utiliza de código, a base de dados local para todos os criar, ler, atualizar e eliminar (CRUD) de operações de tabela.

    Este exemplo executa em conflitos de sincronização de tratamento de erros simples. Um aplicativo real lidaria com vários erros como condições de rede, conflitos de servidor e outras pessoas. Para obter exemplos de código, consulte a [exemplo de sincronização offline].

5. Em seguida, adicione esta função para executar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Decidir quando enviar alterações para o back-end de aplicação móvel chamando **syncContext.push()**. Por exemplo, poderia chamar **syncBackend** num manipulador de eventos de botão vinculado a um botão de sincronização.

## <a name="offline-sync-considerations"></a>Considerações sobre a sincronização offline

No exemplo, o **push** método **syncContext** denomina-se apenas na inicialização de aplicação na função de retorno de chamada para início de sessão.  Num aplicativo do mundo real, também poderia fazer essa funcionalidade de sincronização acionada manualmente ou quando muda o estado de rede.

Quando uma solicitação é executada em relação a uma tabela com pendentes atualizações locais rastreados pelo contexto, que a solicitação de operação automaticamente acionadores um push. Durante a atualização, adicionar e concluir os itens neste exemplo, pode omitir o explícita **push** chamar, uma vez que pode ser redundante.

No código fornecido, todos os registos na tabela todoItem remoto são consultados, mas também é possível filtrar registos ao transmitir um id de consulta e a consulta para **push**. Para obter mais informações, consulte a secção *Sincronização Incremental* na [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Desativar a autenticação

Se não quiser configurar a autenticação antes de sincronização offline para teste, comente a função de retorno de chamada para início de sessão, mas deixar o código dentro uncommented a função de retorno de chamada.  Depois de comente as linhas de início de sessão, o código seguinte:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, o aplicação é sincronizado com o Azure back-end ao executar a aplicação.

## <a name="run-the-client-app"></a>Executar a aplicação de cliente
Com a sincronização offline agora ativada, pode executar a aplicação de cliente, pelo menos, uma vez em cada plataforma para preencher a base de dados do arquivo local. Mais tarde, simular um cenário offline e modificar os dados no arquivo local, enquanto a aplicação está offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Testar o comportamento de sincronização
Nesta secção, modifique o projeto de cliente para simular o cenário offline utilizando um URL de aplicação inválido para o back-end. Quando adicionar ou alterar itens de dados, estas alterações são guardadas no arquivo local, mas não estão sincronizadas com o arquivo de dados de back-end até que a ligação seja restabelecida.

1. No Explorador de soluções, abra o ficheiro de projeto Index e altere o URL de aplicação para apontar para um URL inválido, semelhante ao seguinte código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. No Index. HTML, atualize o CSP `<meta>` elemento com o mesmo URL inválido.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Criar e executar a aplicação de cliente e tenha em atenção que uma exceção está registada na consola, quando a aplicação tenta sincronizar com o back-end após o início de sessão. Os itens novos, adicionar existem apenas no arquivo local até que eles são enviados por push para o back-end móvel. A aplicação de cliente se comporta como se ele está ligado ao back-end.

4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou são persistidos no arquivo local.

5. (Opcional) Utilize o Visual Studio para ver a sua tabela de base de dados do Azure SQL para ver que os dados na base de dados back-end não mudou.

    No Visual Studio, abra **Explorador de servidores**. Navegue até à sua base de dados **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **aberto no SQL Server Object Explorer**. Agora pode navegar para a sua tabela de base de dados SQL e o respetivo conteúdo.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Testar o restabelecimento de ligação para o seu back-end móvel

Nesta secção, voltar a ligar a aplicação para o back-end móvel, o que simula a aplicação volte ao estado online. Quando iniciar sessão, os dados são sincronizados com o seu back-end móvel.

1. Volte a abrir Index e restaurar o URL da aplicação.
2. Abra novamente o Index. HTML e corrija o URL da aplicação no CSP `<meta>` elemento.
3. Recompile e execute a aplicação de cliente. A aplicação tenta sincronizar com o back-end de aplicação móvel depois de início de sessão. Certifique-se de que não existem exceções são registadas na consola de depuração.
4. (Opcional) Ver os dados atualizados com o SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Repare que os dados foram sincronizado entre a base de dados de back-end e o armazenamento local.

    Repare que os dados foram sincronizado entre a base de dados e o armazenamento local e contém os itens adicionados enquanto a aplicação foi desconectada.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Visual Studio Tools para Apache Cordova]

## <a name="next-steps"></a>Passos Seguintes
* Revisão de funcionalidades de sincronização offline, como resolução de conflitos em mais avançadas do [exemplo de sincronização offline]
* Reveja a referência de API de sincronização offline na [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
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
