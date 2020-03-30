---
title: Ativar sincronização offline (iOS)
description: Saiba como utilizar aplicações móveis do Azure App Service para cache e sincronizar dados offline em aplicações iOS.
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: d943213814b999f101a541abb0195a9fdd5a7423
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459179"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Ativar sincronização offline com aplicações móveis iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial cobre a sincronização offline com a funcionalidade de Aplicações Móveis do Serviço de Aplicações Azure para iOS. Com sincronização offline os utilizadores finais podem interagir com uma aplicação móvel para visualizar, adicionar ou modificar dados, mesmo quando não têm ligação à rede. As mudanças são armazenadas numa base de dados local. Depois de o dispositivo voltar a funcionar, as alterações são sincronizadas com a parte traseira remota.

Se esta é a sua primeira experiência com aplicações móveis, deve primeiro completar o tutorial [Criar uma aplicação iOS]. Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte [offline Data Sync em Aplicações Móveis].

## <a name="review-the-client-sync-code"></a><a name="review-sync"></a>Reveja o código de sincronização do cliente
O projeto de cliente que descarregou para o tutorial [create a iOS App] já contém um código que suporta a sincronização offline usando uma base de dados local baseada em Dados Core. Esta secção resume o que já está incluído no código tutorial. Para uma visão geral conceptual da funcionalidade, consulte [offline Data Sync em Aplicações Móveis].

Utilizando a funcionalidade offline de sincronização de dados de Aplicações Móveis, os utilizadores finais podem interagir com uma base de dados local mesmo quando a rede está inacessível. Para utilizar estas funcionalidades na sua aplicação, inicializa o contexto de `MSClient` sincronização e faz referência a uma loja local. Em seguida, referencia a sua tabela através da interface **MSSyncTable.**

Em **QSTodoService.m** (Objectivo-C) ou **ToDoTableViewController.swift** (Swift), note que o tipo de **sincronização** do membro é **MSSyncTable**. A sincronização offline utiliza esta interface de tabela sincronizada em vez de **MSTable**. Quando uma tabela de sincronização é utilizada, todas as operações vão para a loja local e são sincronizadas apenas com a extremidade traseira remota com operações explícitas de empurrar e puxar.

 Para obter uma referência a uma tabela de sincronização, utilize o método **syncTableWithName** em `MSClient`. Para remover a funcionalidade de sincronização offline, utilize **o quadro Com o Nome.**

Antes de serem realizadas quaisquer operações de mesa, a loja local deve ser inicializada. Aqui está o código relevante:

* **Objetivo C.** No método **QSTodoService.init:**

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Rápido.** No método **ToDoTableViewController.viewDidLoad:**

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Este método cria uma loja `MSCoreDataStore` local utilizando a interface, que o Mobile Apps SDK fornece. Em alternativa, pode fornecer uma loja local `MSSyncContextDataSource` diferente implementando o protocolo. Além disso, o primeiro parâmetro do **MSSyncContext** é usado para especificar um manipulador de conflitos. Porque nós `nil`passamos , temos o responsável pelo conflito padrão, que falha em qualquer conflito.

Agora, vamos executar a operação de sincronização real, e obter dados da parte traseira remota:

* **Objetivo C.** `syncData`primeiro empurra novas alterações e, em seguida, chama **pullData** para obter dados da parte traseira remota. Por sua vez, o método **pullData** obtém novos dados que correspondem a uma consulta:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift:**
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Na versão Objective-C, `syncData`in, chamamos **pushWithCompletion** no contexto de sincronização. Este método é `MSSyncContext` um membro (e não a própria tabela de sincronização) porque empurra mudanças em todas as tabelas. Apenas os registos que tenham sido modificados de alguma forma localmente (através de operações cud) são enviados para o servidor. Em seguida, o helper **pullData** é chamado, que chama **MSSyncTable.pullWithQuery** para recuperar dados remotos e armazená-lo na base de dados local.

Na versão Swift, porque a operação push não era estritamente necessária, não há nenhuma chamada para **empurrarWithCompletion**. Se houver alterações pendentes no contexto de sincronização para a tabela que está a fazer uma operação de impulso, puxe sempre primeiro um empurrão. No entanto, se tiver mais do que uma tabela de sincronização, o melhor é ligar explicitamente para garantir que tudo é consistente entre tabelas relacionadas.

Nas versões Objective-C e Swift, pode utilizar o método **pullWithQuery** para especificar uma consulta para filtrar os registos que pretende recuperar. Neste exemplo, a consulta recupera todos os `TodoItem` registos na mesa remota.

O segundo parâmetro de **pullWithQuery** é um ID de consulta que é usado para *sincronização incremental*. A sincronização incremental recupera apenas registos que foram modificados desde a última sincronização, utilizando o carimbo de tempo do `UpdatedAt` registo (chamado `updatedAt` na loja local.) O ID de consulta deve ser uma cadeia descritiva que é única para cada consulta lógica na sua aplicação. Para não sincronizar incremental, passe `nil` como o ID de consulta. Esta abordagem pode ser potencialmente ineficiente, porque recupera todos os registos em cada operação de puxar.

A aplicação Objective-C sincroniza-se quando modifica ou adiciona dados, quando um utilizador executa o gesto de atualização e no lançamento.

A aplicação Swift sincroniza-se quando o utilizador executa o gesto de atualização e no lançamento.

Uma vez que a aplicação sincroniza sempre que os dados são modificados (Objectivo-C) ou sempre que a aplicação começa (Objective-C e Swift), a aplicação assume que o utilizador está online. Numa secção posterior, irá atualizar a aplicação para que os utilizadores possam editar mesmo quando estão offline.

## <a name="review-the-core-data-model"></a><a name="review-core-data"></a>Reveja o modelo De dados fundamentais
Quando utilizar a loja offline Core Data, deve definir tabelas e campos específicos no seu modelo de dados. A aplicação de amostrajá inclui um modelo de dados com o formato certo. Nesta secção, caminhamos por estas mesas para mostrar como são usadas.

Abrir **QSDataModel.xcdatamodeld**. Quatro tabelas são definidas- três que são usadas pelo SDK e uma que é usada para os itens a fazer em si:
  * MS_TableOperations: Rastreia os itens que precisam de ser sincronizados com o servidor.
  * MS_TableOperationErrors: Rastreia quaisquer erros que ocorram durante a sincronização offline.
  * MS_TableConfig: Rastreia a última hora atualizada para a última operação de sincronização para todas as operações de puxar.
  * TodoItem: Armazena os itens a fazer. As colunas do sistema **criadasAt**, **atualizadoAt**, e **versão** são propriedades do sistema opcionais.

> [!NOTE]
> As Aplicações Móveis SDK reservam nomes de colunas que começam com "**``**". Não utilize este prefixo com outra coisa que não as colunas do sistema. Caso contrário, os nomes das colunas são modificados quando utiliza a parte traseira remota.
>
>

Quando utilizar a função de sincronização offline, defina as três tabelas do sistema e a tabela de dados.

### <a name="system-tables"></a>Tabelas de sistema

**MS_TableOperations**  

![MS_TableOperations atributos de mesa][defining-core-data-tableoperations-entity]

| Atributo | Tipo |
| --- | --- |
| ID | Inteiro 64 |
| itemId | Cadeia |
| propriedades | Dados Binários |
| tabela | Cadeia |
| tabelaKind | Inteiro 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors atributos de tabela][defining-core-data-tableoperationerrors-entity]

| Atributo | Tipo |
| --- | --- |
| ID |Cadeia |
| operationId |Inteiro 64 |
| propriedades |Dados Binários |
| tabelaKind |Inteiro 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atributo | Tipo |
| --- | --- |
| ID |Cadeia |
| key |Cadeia |
| chaveTipo |Inteiro 64 |
| tabela |Cadeia |
| valor |Cadeia |

### <a name="data-table"></a>Tabela de dados

**TodoItem**

| Atributo | Tipo | Nota |
| --- | --- | --- |
| ID | Corda, marcada necessária |Chave primária na loja remota |
| completar | Booleano | Campo de item para fazer |
| texto |Cadeia |Campo de item para fazer |
| criadoAt | Date | (opcional) Mapas para criar Propriedade do **sistemaAt** |
| atualizadoAt | Date | (opcional) Mapas para atualizar Propriedade do **sistemaAt** |
| versão | Cadeia | (opcional) Usado para detetar conflitos, mapas para versão |

## <a name="change-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Alterar o comportamento sincronizado da app
Nesta secção, modifica a aplicação para que não se sincronize no arranque da aplicação ou quando insere e atualize itens. Sincroniza-se apenas quando é realizado o botão de gesto de atualização.

**Objetivo C:**

1. Em **QSTodoListViewController.m,** altere o método **viewDidLoad** para remover a chamada `[self refresh]` para o final do método. Agora os dados não estão sincronizados com o servidor no início da aplicação. Em vez disso, está sincronizado com o conteúdo da loja local.
2. Em **QSTodoService.m,** modifique `addItem` a definição de modo a não sincronizar após a inserção do artigo. Retire `self syncData` o bloco e substitua-o pelo seguinte:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modificar a `completeItem` definição de como mencionado anteriormente. Retire o `self syncData` bloco e substitua-o pelo seguinte:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift:**

Em `viewDidLoad` **ToDoTableViewController.swift,** comente as duas linhas aqui mostradas, para parar de sincronizar no arranque da aplicação. No momento desta escrita, a aplicação Swift Todo não atualiza o serviço quando alguém adiciona ou completa um item. Atualiza o serviço apenas no início da aplicação.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-the-app"></a><a name="test-app"></a>Testar a aplicação
Nesta secção, liga-se a um URL inválido para simular um cenário offline. Quando adiciona itens de dados, são mantidos na loja local Core Data, mas não estão sincronizados com a parte traseira da aplicação móvel.

1. Mude o URL da aplicação móvel em **QSTodoService.m** para um URL inválido e volte a executar a aplicação:

   **Objetivo C.** Em QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Rápido.** Em ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adicione alguns itens para fazer. Deixe o simulador (ou feche à força a aplicação) e reinicie-o. Verifique se as suas alterações persistem.

3. Veja o conteúdo da tabela **TodoItem** remota:
   * Para um lado traseiro do Node.js, vá ao [portal Azure](https://portal.azure.com/) e, na sua extremidade de aplicação móvel, clique em **Easy Tables** > **TodoItem**.  
   * Para uma parte traseira .NET, utilize uma ferramenta SQL, como o SQL Server Management Studio, ou um cliente REST, como Fiddler ou Postman.  

4. Verifique se os novos itens *não* foram sincronizados com o servidor.

5. Mude o URL de volta para o correto em **QSTodoService.m**, e volte a executar a aplicação.

6. Execute o gesto de atualização puxando para baixo a lista de itens.  
Um girador de progresso é exibido.

7. Veja novamente os dados **do TodoItem.** Os novos e alterados itens a fazer devem agora ser exibidos.

## <a name="summary"></a>Resumo
Para suportar a funcionalidade de `MSSyncTable` sincronização offline, usamos a interface e rubricamos `MSClient.syncContext` com uma loja local. Neste caso, a loja local era uma base de dados baseada em Dados Nucleares.

Quando utilizar uma loja local Core Data, deve definir várias tabelas com as [propriedades corretas](#review-core-data)do sistema .

As operações normais de criação, leitura, atualização e exclusão (CRUD) para aplicações móveis funcionam como se a aplicação ainda estivesse ligada, mas todas as operações ocorrem contra a loja local.

Quando sincronizámos a loja local com o servidor, usamos o método **MSSyncTable.pullWithQuery.**

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de dados offline em aplicações móveis]
* [Cloud Cover: Offline Sync in Azure Mobile Services] \(O vídeo é sobre Serviços Móveis, mas a sincronização offline das Aplicações Móveis funciona de forma semelhante.\)

<!-- URLs. -->


[Criar uma app iOS]: app-service-mobile-ios-get-started.md
[Sincronização de dados offline em aplicações móveis]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
