---
title: Ativar a sincronização offline com aplicações móveis iOS | Documentos da Microsoft
description: Saiba como utilizar aplicações móveis do App Service do Azure para a cache e sincronizar dados offline em aplicações de iOS.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 1283f812799fe71ef6987dbc7fab092aed4d3417
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435138"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Ativar a sincronização offline com aplicações móveis iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial abrange a sincronização offline com a funcionalidade de aplicações móveis do serviço de aplicações do Azure para iOS. Os utilizadores finais de sincronização offline, pode interagir com uma aplicação móvel para ver, adicionar ou modificar dados, mesmo quando elas têm nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Depois do dispositivo estiver online novamente, as alterações são sincronizadas com o back-end remoto.

Se esta for a sua primeira experiência com as aplicações móveis, deve primeiro concluir o tutorial [criar uma aplicação iOS]. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte [sincronização de dados Offline em aplicações móveis].

## <a name="review-sync"></a>Rever o código de sincronização do cliente
O projeto de cliente que transferiu para o [criar uma aplicação iOS] tutorial já contém o código que suporta a sincronização offline, usando um banco de dados local com a base de dados principais. Esta secção resume o que já inclui o código do tutorial. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline em aplicações móveis].

Utilizar a funcionalidade de sincronização offline de dados de aplicações móveis, os utilizadores finais podem interagir com um banco de dados local, mesmo quando a rede não está acessível. Para usar esses recursos em seu aplicativo, inicializar o contexto de sincronização de `MSClient` e fazer referência a um armazenamento local. Em seguida, referenciar a tabela através da **MSSyncTable** interface.

Na **QSTodoService.m** (Objective-C) ou **ToDoTableViewController.swift** (Swift), tenha em atenção que o tipo do membro **syncTable** é **MSSyncTable** . Sincronização offline usa essa interface da tabela de sincronização em vez de **MSTable**. Quando uma tabela de sincronização é utilizada, todas as operações de ir para o armazenamento local e são sincronizadas apenas com o back-end remoto com operações de push e pull explícitas.

 Para obter uma referência a uma tabela de sincronização, utilize o **syncTableWithName** método no `MSClient`. Para remover a funcionalidade de sincronização offline, utilize **tableWithName** em vez disso.

Antes de quaisquer operações de tabela podem ser realizadas, o arquivo local tem de ser inicializado. Eis o código relevante:

* **Objective-C**. Na **QSTodoService.init** método:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. Na **ToDoTableViewController.viewDidLoad** método:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Este método cria um armazenamento local utilizando o `MSCoreDataStore` interface, que fornece o SDK de aplicações móveis. Em alternativa, pode fornecer um armazenamento local diferente implementando o `MSSyncContextDataSource` protocolo. Além disso, o primeiro parâmetro de **MSSyncContext** é utilizado para especificar um manipulador de conflito. Uma vez que estamos passaram `nil`, obtemos o manipulador de conflito de padrão, o que ocorre uma falha de conflito.

Agora, vamos executar a operação de sincronização real e obter dados de back-end remoto:

* **Objective-C**. `syncData` primeiro envia novas alterações e, em seguida, chama **pullData** para obter dados de back-end remoto. Por sua vez, o **pullData** método obtém dados novos que corresponda a uma consulta:

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
* **Swift**:
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

Na versão Objective-C, no `syncData`, primeiro chamamos **pushWithCompletion** no contexto de sincronização. Este método é um membro do `MSSyncContext` (e não a tabela de sincronização em si) porque ele envia as alterações em todas as tabelas. Apenas os registos que foram modificados de alguma forma localmente (por meio de operações CUD) são enviados para o servidor. Em seguida, o programa auxiliar **pullData** é chamado, que invoca **MSSyncTable.pullWithQuery** para obter os dados remotos e armazená-los no banco de dados local.

Na versão Swift, porque a operação de push não é estritamente necessária, não há nenhuma chamada para **pushWithCompletion**. Se existirem quaisquer alterações pendentes no contexto de sincronização para a tabela que está a fazer uma operação de push, pull sempre emite um push pela primeira vez. No entanto, se tiver mais de uma tabela de sincronização, é melhor chamar explicitamente o push para se certificar de que tudo o que é consistente em tabelas relacionadas.

Tanto o Objective-C e versões Swift, pode utilizar o **pullWithQuery** método para especificar uma consulta para filtrar os registos que pretende recuperar. Neste exemplo, a consulta devolve todos os registos na remoto `TodoItem` tabela.

O segundo parâmetro do **pullWithQuery** é um ID de consulta que é utilizado para *sincronização incremental*. Sincronização incremental obtém apenas os registos que foram modificados desde a última sincronização com o registo `UpdatedAt` carimbo de data / hora (chamado `updatedAt` no arquivo local.) O ID de consulta deve ser uma cadeia de caracteres descritiva exclusiva para cada consulta lógica na sua aplicação. Opção de desativar a sincronização incremental, passar `nil` como o ID de consulta. Esta abordagem pode ser potencialmente ineficiente, porque ele recupera todos os registos de cada operação de solicitação.

A aplicação de Objective-C sincroniza quando modifica ou adicionar dados, quando um utilizador executa o gesto de atualização e no lançamento.

A aplicação Swift sincroniza quando o utilizador executa o gesto de atualização e no lançamento.

Uma vez que as sincronizações de aplicação sempre que os dados são modificados (Objective-C), ou sempre que a aplicação for iniciada (Objective-C e Swift), a aplicação parte do princípio de que o utilizador está online. Numa seção posterior, irá atualizar a aplicação para que os usuários podem editar, mesmo quando estão offline.

## <a name="review-core-data"></a>Reveja o modelo de dados principais
Quando utiliza o armazenamento offline de dados principais, tem de definir determinadas tabelas e campos no seu modelo de dados. A aplicação de exemplo já inclui um modelo de dados com o formato correto. Nesta secção, vamos analisar estas tabelas para mostrar como elas são usadas.

Open **QSDataModel.xcdatamodeld**. Quatro tabelas são definidas – três que são utilizados pelo SDK e outro que é utilizado para a ação a efetuar itens próprios:
  * MS_TableOperations: Controla os itens que devem ser sincronizadas com o servidor.
  * MS_TableOperationErrors: Controla todos os erros que ocorrem durante a sincronização offline.
  * MS_TableConfig: Controla a última atualizado tempo para a última operação de sincronização para todas as operações de extração.
  * TodoItem: Armazena os itens de tarefas. As colunas de sistema **createdAt**, **updatedAt**, e **versão** são propriedades do sistema opcional.

> [!NOTE]
> O SDK de aplicações móveis reserva-se os nomes de coluna que começam por "**``**". Não utilize este prefixo com algo diferente de colunas de sistema. Caso contrário, os nomes de coluna são modificados ao utilizar o back-end remoto.
>
>

Quando utiliza a funcionalidade de sincronização offline, defina as tabelas de três sistema e a tabela de dados.

### <a name="system-tables"></a>Tabelas de sistema

**MS_TableOperations**  

![Atributos da tabela de MS_TableOperations][defining-core-data-tableoperations-entity]

| Atributo | Type |
| --- | --- |
| ID | Número inteiro de 64 |
| itemId | String |
| propriedades | Dados binários |
| tabela | String |
| tableKind | Número inteiro de 16 |


**MS_TableOperationErrors**

 ![Atributos da tabela de MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atributo | Type |
| --- | --- |
| ID |String |
| operationId |Número inteiro de 64 |
| propriedades |Dados binários |
| tableKind |Número inteiro de 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atributo | Type |
| --- | --- |
| ID |String |
| key |String |
| keyType |Número inteiro de 64 |
| tabela |String |
| valor |String |

### <a name="data-table"></a>Tabela de dados

**TodoItem**

| Atributo | Type | Nota |
| --- | --- | --- |
| ID | Cadeia de caracteres, marcada como necessária |chave primária no arquivo remoto |
| Concluir | Booleano | Campo de item de tarefas pendentes |
| texto |String |Campo de item de tarefas pendentes |
| createdAt | Date | (opcional) É mapeado para **createdAt** propriedade do sistema |
| updatedAt | Date | (opcional) É mapeado para **updatedAt** propriedade do sistema |
| versão | String | (opcional) Usado para detectar conflitos, mapas para a versão |

## <a name="setup-sync"></a>Alterar o comportamento de sincronização da aplicação
Nesta secção, modifique a aplicação para que ele não sincroniza no início da aplicação ou quando insere e atualizar itens. Ele sincroniza apenas quando o botão de gesto de atualização é executado.

**Objective-C**:

1. Na **QSTodoListViewController.m**, altere a **viewDidLoad** método para remover a chamada para `[self refresh]` no final do método. Agora, os dados não estão sincronizados com o servidor no início da aplicação. Em vez disso, ele está sincronizado com o conteúdo do arquivo local.
2. Na **QSTodoService.m**, modificar a definição de `addItem` para que ele não sincronizado depois do item é inserido. Remover o `self syncData` bloquear e substituí-lo com o seguinte:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modificar a definição de `completeItem` como mencionado anteriormente. Remover o bloco para `self syncData` e substituí-lo com o seguinte:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

Na `viewDidLoad`, na **ToDoTableViewController.swift**, comente as duas linhas mostrado aqui, para parar a sincronização no início da aplicação. No momento da redação deste artigo, a aplicação Swift fazer não atualizar o serviço quando alguém adiciona ou concluir um item. Atualiza o serviço só no início da aplicação.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testar a aplicação
Nesta secção, vai ligar a uma URL inválida para simular o cenário offline. Ao adicionar itens de dados, estão contidos num local do arquivo de dados principal, mas eles não estão sincronizados com o back-end de aplicações móveis.

1. Altere o URL de aplicações móveis no **QSTodoService.m** para uma URL inválido e execute novamente a aplicação:

   **Objective-C**. In QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. No ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adicione alguns itens a fazer. Sair o simulator (ou fechar a forçar a aplicação) e, em seguida, reiniciá-lo. Certifique-se de que as alterações persistem.

3. Exibir o conteúdo da mesma **TodoItem** tabela:
   * Para um back-end de node. js, vá para o [portal do Azure](https://portal.azure.com/) e, na sua aplicação móvel back-end, clique em **tabelas simples** > **TodoItem**.  
   * Para voltar um .NET terminar, utilize uma ferramenta SQL, como o SQL Server Management Studio, ou um cliente REST, como o Fiddler ou o Postman.  

4. Certifique-se de que os novos itens têm *não* foi sincronizado com o servidor.

5. Altere o URL de volta para o correto na **QSTodoService.m**e volte a executar a aplicação.

6. Execute o gesto de atualização ao extrair a lista de itens.  
Um controlo giratório de progresso é apresentado.

7. Ver os **TodoItem** dados novamente. Os itens de tarefas novas e alteradas deverá ser agora apresentados.

## <a name="summary"></a>Resumo
Para suportar a funcionalidade de sincronização offline, usamos o `MSSyncTable` interface e inicializado `MSClient.syncContext` com um armazenamento local. Neste caso, o armazenamento local foi uma base de dados com base em dados principais.

Quando utilizar um armazenamento local de dados principais, tem de definir várias tabelas com o [corrigir as propriedades do sistema](#review-core-data).

O normal de criar, ler, atualizar e eliminar (CRUD) de operações para o trabalho de aplicações móveis, como se a aplicação ainda está ligada, mas todas as operações ocorrem em relação ao armazenamento local.

Quando o armazenamento local, sincronizados com o servidor, usamos o **MSSyncTable.pullWithQuery** método.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de dados offline em aplicações móveis]
* [Cloud Cover: Sincronização offline nos serviços móveis do Azure] \(o vídeo é sobre os serviços móveis, mas Mobile Apps offline sincroniza funciona de forma semelhante.\)

<!-- URLs. -->


[Criar uma aplicação iOS]: app-service-mobile-ios-get-started.md
[Sincronização de dados offline em aplicações móveis]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Sincronização offline nos serviços móveis do Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
