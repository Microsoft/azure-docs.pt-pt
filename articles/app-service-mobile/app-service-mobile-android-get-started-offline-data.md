---
title: Permitir sincronização offline para a sua aplicação de Mobile do Azure (Android)
description: Aprenda a utilizar Mobile Apps do App Service para cache e sincronizar dados offline em seu aplicativo Android
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
ms.openlocfilehash: 3fe5b176d864fd4cdd1ff49d8c064495663aa3b0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443568"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Descrição geral
Este tutorial abrange a funcionalidade de sincronização offline de aplicações móveis do Azure para Android. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Depois do dispositivo estiver online novamente, essas alterações são sincronizadas com o back-end remoto.

Se esta for a sua primeira experiência com aplicações móveis do Azure, deve primeiro concluir o tutorial [criar uma aplicação Android]. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline em aplicações móveis do Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Atualizar a aplicação para suportar a sincronização offline
Com a sincronização offline, de leitura para e de escrita de um *tabela de sincronização* (com o *IMobileServiceSyncTable* interface), que faz parte de um **SQLite** base de dados no seu dispositivo.

Para enviar e extrair as alterações entre o dispositivo e os serviços móveis do Azure, utilize um *contexto de sincronização* (*MobileServiceClient.SyncContext*), que inicializar com o banco de dados local para armazenar dados localmente.

1. Na `TodoActivity.java`, comente a definição existente de `mToDoTable` e anule os comentários a versão de tabela de sincronização:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. Na `onCreate` método, comente a inicialização existente de `mToDoTable` e anule os comentários desta definição:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. Na `refreshItemsFromTable` comente a definição de `results` e anule os comentários desta definição:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Comente a definição de `refreshItemsFromMobileServiceTable`.
5. Anule os comentários a definição de `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Anule os comentários a definição de `sync`:
   
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
Nesta secção, teste o comportamento com Wi-Fi em e, em seguida, desative a Wi-Fi para criar um cenário offline.

Ao adicionar itens de dados, eles estão contidos no arquivo de SQLite local, mas não sincronizados com o serviço móvel, até que pressionar os **atualizar** botão. Outras aplicações podem ter requisitos diferentes em relação a quando dados têm de ser sincronizados, mas para fins de demonstração neste tutorial tem o utilizador explicitamente solicitá-la.

Quando pressiona o botão, inicia uma nova tarefa em segundo plano. Em primeiro lugar, ele leva todas as alterações feitas no arquivo local usando o contexto de sincronização, em seguida, alterar todos os pedidos de dados do Azure para a tabela local.

### <a name="offline-testing"></a>Testes offline
1. Colocar o dispositivo ou o simulador na *modo de avião*. Esta ação cria um cenário offline.
2. Adicionar algumas *ToDo* itens, ou marca alguns itens como concluídos. O dispositivo ou o simulador de sair (ou fechar a forçar a aplicação) e reinicie. Certifique-se de que as suas alterações foram guardadas no dispositivo porque eles são guardados no arquivo local do SQLite.
3. Ver os conteúdos do Azure *TodoItem* como a tabela com uma ferramenta SQL *SQL Server Management Studio*, ou um cliente REST como *Fiddler* ou  *Postman*. Certifique-se de que os novos itens têm *não* foi sincronizado com o servidor
   
       + Para um back-end de node. js, vá para o [portal do Azure](https://portal.azure.com/), e na sua aplicação móveis clique em back-end **tabelas simples** > **TodoItem** para exibir o conteúdo do `TodoItem`tabela.
       + Para um back-end de .NET, exibir o conteúdo de tabela com o SQL, tais como ferramenta *SQL Server Management Studio*, ou um cliente REST como *Fiddler* ou *Postman*.
4. Ative o Wi-Fi no dispositivo ou simulador. Em seguida, prima a **atualizar** botão.
5. Ver os dados de TodoItem novamente no portal do Azure. O novo e alterado TodoItems deverá ser exibida.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline em Aplicações Móveis do Azure]
* [Cloud Cover: Sincronização offline nos serviços móveis do Azure] \(Nota: o vídeo está nos serviços móveis, mas a sincronização offline funciona de forma semelhante, em aplicações móveis do Azure\)

<!-- URLs. -->

[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md

[Criar uma aplicação Android]: app-service-mobile-android-get-started.md

[Cloud Cover: Sincronização offline nos serviços móveis do Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

