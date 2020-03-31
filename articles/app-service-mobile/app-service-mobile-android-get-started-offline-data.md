---
title: Ativar sincronização offline (Android)
description: Saiba como utilizar aplicações móveis do Serviço app para cache e sincronizar dados offline na sua aplicação Android.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461628"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Ativar sincronização offline para a sua aplicação móvel Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial cobre a funcionalidade de sincronização offline de Aplicações Móveis Azure para Android. A sincronização offline permite que&mdash;os utilizadores finais interajam com uma visualização de aplicações móveis, adicionando ou modificando dados&mdash;mesmo quando não há ligação de rede. As mudanças são armazenadas numa base de dados local. Uma vez que o dispositivo esteja novamente on-line, estas alterações são sincronizadas com o backend remoto.

Se esta é a sua primeira experiência com aplicações móveis Azure, deve primeiro completar o tutorial [Criar uma Aplicação Android]. Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Offline Data Sync em Aplicações Móveis Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Atualize a app para suportar sincronização offline
Com sincronização offline, lê-se e escreve a partir de uma *tabela de sincronização* (utilizando a interface *IMobileServiceSyncTable),* que faz parte de uma base de dados **SQLite** no seu dispositivo.

Para empurrar e puxar as alterações entre o dispositivo e os Serviços Móveis Azure, utiliza um contexto de *sincronização* *(MobileServiceClient.SyncContext),* que rubrica com a base de dados local para armazenar dados localmente.

1. Em `TodoActivity.java`, comentar a definição existente de `mToDoTable` e descomentar a versão da tabela sincronizada:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. No `onCreate` método, comente a inicialização `mToDoTable` existente e não comente esta definição:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. Em `refreshItemsFromTable` comentários, a `results` definição de e descomentar esta definição:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Comentar a definição de `refreshItemsFromMobileServiceTable`.
5. Não comentar a `refreshItemsFromMobileServiceTableSyncTable`definição de:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Não comentar a `sync`definição de:
   
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
Nesta secção, testa-se o comportamento com wi-fi ligado e, em seguida, desliga o Wi-Fi para criar um cenário offline.

Quando adiciona itens de dados, são mantidos na loja Local SQLite, mas não sincronizados com o serviço móvel até premir o botão **Refresh.** Outras aplicações podem ter diferentes requisitos em relação ao momento em que os dados precisam de ser sincronizados, mas para fins de demonstração este tutorial tem o utilizador expressamente solicitado.

Quando carregas nesse botão, começa uma nova tarefa de fundo. Primeiro empurra todas as alterações feitas para a loja local usando o contexto de sincronização, em seguida, puxa todos os dados alterados de Azure para a mesa local.

### <a name="offline-testing"></a>Testes offline
1. Coloque o dispositivo ou simulador no *modo avião*. Isto cria um cenário offline.
2. Adicione alguns itens *ToDo* ou marque alguns itens como completos. Saia do dispositivo ou simulador (ou feche à força a aplicação) e reinicie. Verifique se as suas alterações foram persistidas no dispositivo porque estão retidas na loja Local SQLite.
3. Veja o conteúdo da tabela Azure *TodoItem* com uma ferramenta *SQL como o SQL Server Management Studio,* ou um cliente REST como *Fiddler* ou *Postman*. Verifique se os novos itens *não* foram sincronizados com o servidor
   
       + Para um backend Node.js, vá ao [portal Azure](https://portal.azure.com/), e no seu backend de `TodoItem` aplicação móvel clique em Easy **Tables** > **TodoItem** para ver o conteúdo da tabela.
       + Para um backend .NET, veja o conteúdo da tabela com uma ferramenta *SQL como o SQL Server Management Studio,* ou um cliente REST como *Fiddler* ou *Postman*.
4. Ligue o Wi-Fi no dispositivo ou simulador. Em seguida, prima o botão **Refresh.**
5. Veja novamente os dados do TodoItem no portal Azure. Os novos e alterados TodoItems devem agora aparecer.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de Dados Offline nas Aplicações Móveis do Azure]
* [Cloud Cover: Offline Sync in Azure Mobile Services] \(nota: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de forma semelhante em Aplicações Móveis Azure\)

<!-- URLs. -->

[Sincronização de Dados Offline nas Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md

[Criar uma aplicação Android]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

