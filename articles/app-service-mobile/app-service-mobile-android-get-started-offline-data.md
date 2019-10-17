---
title: Habilitar sincronização offline para seu aplicativo móvel do Azure (Android)
description: Saiba como usar os aplicativos móveis do serviço de aplicativo para armazenar em cache e sincronizar dados offline em seu aplicativo Android
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9c845c09c6b05436017f98323dfa78185cd58aa6
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388988"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Habilitar sincronização offline para seu aplicativo móvel Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial aborda o recurso de sincronização offline de aplicativos móveis do Azure para Android. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel @ no__t-0viewing, adicionando ou modificando dados @ no__t-1even quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, essas alterações serão sincronizadas com o back-end remoto.

Se esta for sua primeira experiência com os aplicativos móveis do Azure, você deve primeiro concluir o tutorial [criar um aplicativo Android]. Se você não usar o projeto baixado do servidor de início rápido, deverá adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de Dados Offline em Aplicações Móveis do Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Atualizar o aplicativo para dar suporte à sincronização offline
Com a sincronização offline, você lê e grava a partir de uma *tabela de sincronização* (usando a interface *IMobileServiceSyncTable* ), que faz parte de um banco de dados **SQLite** em seu dispositivo.

Para enviar por push e efetuar pull de alterações entre o dispositivo e os serviços móveis do Azure, você usa um *contexto de sincronização* (*MobileServiceClient. SyncContext*), que é inicializado com o banco de dados local para armazenar os dados localmente.

1. Em `TodoActivity.java`, comente a definição existente de `mToDoTable` e remova a marca de comentário da versão da tabela de sincronização:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. No método `onCreate`, comente a inicialização existente de `mToDoTable` e remova a marca de comentário desta definição:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. No `refreshItemsFromTable`, comente a definição de `results` e remova a marca de comentário desta definição:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Comente a definição de `refreshItemsFromMobileServiceTable`.
5. Remova a marca de comentário da definição de `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Remova a marca de comentário da definição de `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testar a aplicação
Nesta seção, você testará o comportamento com WiFi ativado e, em seguida, desligará o WiFi para criar um cenário offline.

Quando você adiciona itens de dados, eles são mantidos no armazenamento local do SQLite, mas não sincronizados com o serviço móvel até que você pressione o botão **Atualizar** . Outros aplicativos podem ter requisitos diferentes sobre quando os dados precisam ser sincronizados, mas para fins de demonstração, este tutorial tem o usuário solicitá-lo explicitamente.

Quando você pressiona esse botão, uma nova tarefa em segundo plano é iniciada. Primeiro, ele envia por push todas as alterações feitas no armazenamento local usando o contexto de sincronização e, em seguida, efetua pull de todos os dados alterados do Azure para a tabela local.

### <a name="offline-testing"></a>Teste offline
1. Coloque o dispositivo ou simulador no *modo avião*. Isso cria um cenário offline.
2. Adicione alguns itens de *tarefas pendentes* ou marque alguns itens como concluídos. Encerre o dispositivo ou o simulador (ou feche o aplicativo de maneira forçada) e reinicie. Verifique se as alterações foram persistidas no dispositivo porque elas são mantidas no repositório SQLite local.
3. Exiba o conteúdo da tabela *TodoItem* do Azure com uma ferramenta SQL, como *SQL Server Management Studio*, ou um cliente REST, como o *Fiddler* ou o *postmaster*. Verifique se os novos itens *não* foram sincronizados com o servidor
   
       + Para um back-end do node. js, vá para o [portal do Azure](https://portal.azure.com/)e, no back-end do aplicativo móvel, clique em **tabelas fáceis** > **TodoItem** para exibir o conteúdo da tabela `TodoItem`.
       + Para um back-end do .NET, exiba o conteúdo da tabela com uma ferramenta SQL, como *SQL Server Management Studio*, ou um cliente REST, como o *Fiddler* ou o *postmaster*.
4. Ative o WiFi no dispositivo ou simulador. Em seguida, pressione o botão **Atualizar** .
5. Exiba os dados de TodoItem novamente na portal do Azure. O TodoItems novo e o alterado agora devem aparecer.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Cobertura de nuvem: sincronização offline nos serviços móveis do Azure] \(note: o vídeo está nos serviços móveis, mas a sincronização offline funciona de maneira semelhante nos aplicativos móveis do Azure @ no__t-2

<!-- URLs. -->

[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md

[Criar um aplicativo Android]: app-service-mobile-android-get-started.md

[Cobertura de nuvem: sincronização offline nos serviços móveis do Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

