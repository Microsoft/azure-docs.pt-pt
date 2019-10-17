---
title: Habilitar a sincronização offline com aplicativos móveis iOS | Microsoft Docs
description: Saiba como usar Azure App aplicativos móveis de serviço para armazenar em cache e sincronizar dados offline em aplicativos iOS.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f29a28f9a80b64ef0a6890fa8fc7ecd0ca205e66
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388763"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Habilitar a sincronização offline com aplicativos móveis iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial aborda a sincronização offline com o recurso aplicativos móveis do serviço de Azure App para iOS. Com a sincronização offline os usuários finais podem interagir com um aplicativo móvel para exibir, adicionar ou modificar dados, mesmo quando não têm conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, as alterações serão sincronizadas com o back-end remoto.

Se esta for sua primeira experiência com aplicativos móveis, você deve primeiro concluir o tutorial [criar um aplicativo IOS]. Se você não usar o projeto do servidor de início rápido baixado, deverá adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte [sincronização de dados offline em aplicativos móveis].

## <a name="review-sync"></a>Examinar o código de sincronização do cliente
O projeto de cliente que você baixou para o tutorial [criar um aplicativo IOS] já contém um código que dá suporte à sincronização offline usando um banco de dados baseado no núcleo local. Esta seção resume o que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, consulte [sincronização de dados offline em aplicativos móveis].

Usando o recurso de sincronização de dados offline de aplicativos móveis, os usuários finais podem interagir com um banco de dado local mesmo quando a rede está inacessível. Para usar esses recursos em seu aplicativo, você inicializa o contexto de sincronização de `MSClient` e faz referência a um repositório local. Em seguida, você faz referência à tabela por meio da interface **MSSyncTable** .

Em **QSTodoService. m** (Objective-C) ou **ToDoTableViewController. Swift** (Swift), observe que o tipo do membro **SyncTable** é **MSSyncTable**. A sincronização offline usa essa interface de tabela de sincronização em vez de **MSTable**. Quando uma tabela de sincronização é usada, todas as operações vão para o repositório local e são sincronizadas somente com o back-end remoto com operações explícitas de push e pull.

 Para obter uma referência a uma tabela de sincronização, use o método **syncTableWithName** em `MSClient`. Para remover a funcionalidade de sincronização offline, use **tableWithName** em vez disso.

Antes que qualquer operação de tabela possa ser executada, o repositório local deve ser inicializado. Este é o código relevante:

* **Objective-C**. No método **QSTodoService. init** :

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. No método **ToDoTableViewController. viewDidLoad** :

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Esse método cria um repositório local usando a interface `MSCoreDataStore`, que o SDK de aplicativos móveis fornece. Como alternativa, você pode fornecer um repositório local diferente implementando o protocolo `MSSyncContextDataSource`. Além disso, o primeiro parâmetro de **MSSyncContext** é usado para especificar um manipulador de conflitos. Como passamos `nil`, obtemos o manipulador de conflitos padrão, que falha em qualquer conflito.

Agora, vamos executar a operação de sincronização real e obter dados do back-end remoto:

* **Objective-C**. `syncData` primeiro envia novas alterações e, em seguida, chama **pullData** para obter dados do back-end remoto. Por sua vez, o método **pullData** Obtém novos dados que correspondem a uma consulta:

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

Na versão Objective-C, em `syncData`, primeiro chamamos **pushWithCompletion** no contexto de sincronização. Esse método é membro de `MSSyncContext` (e não da tabela de sincronização em si) porque envia por push as alterações em todas as tabelas. Somente os registros que foram modificados de alguma forma localmente (por meio de operações CUD) são enviados para o servidor. Em seguida, o auxiliar **pullData** é chamado, que chama **MSSyncTable. pullWithQuery** para recuperar dados remotos e armazená-los no banco de dado local.

Na versão Swift, como a operação de envio por push não era estritamente necessária, não há nenhuma chamada para **pushWithCompletion**. Se houver alterações pendentes no contexto de sincronização para a tabela que está fazendo uma operação de envio por push, o pull sempre emitirá um push primeiro. No entanto, se você tiver mais de uma tabela de sincronização, é melhor chamar explicitamente push para garantir que tudo seja consistente entre as tabelas relacionadas.

Nas versões Objective-C e Swift, você pode usar o método **pullWithQuery** para especificar uma consulta para filtrar os registros que deseja recuperar. Neste exemplo, a consulta recupera todos os registros na tabela `TodoItem` remota.

O segundo parâmetro de **pullWithQuery** é uma ID de consulta que é usada para *sincronização incremental*. A sincronização incremental recupera somente os registros que foram modificados desde a última sincronização, usando o carimbo de data/hora `UpdatedAt` do registro (chamado `updatedAt` no repositório local.) A ID de consulta deve ser uma cadeia de caracteres descritiva que seja exclusiva para cada consulta lógica em seu aplicativo. Para recusar a sincronização incremental, passe `nil` como a ID de consulta. Essa abordagem pode ser potencialmente ineficiente, pois recupera todos os registros em cada operação de pull.

O aplicativo Objective-C é sincronizado quando você modifica ou adiciona dados, quando um usuário executa o gesto de atualização e na inicialização.

O aplicativo Swift é sincronizado quando o usuário executa o gesto de atualização e na inicialização.

Como o aplicativo é sincronizado sempre que os dados são modificados (Objective-C) ou sempre que o aplicativo é iniciado (Objective-C e Swift), o aplicativo pressupõe que o usuário está online. Em uma seção posterior, você atualizará o aplicativo para que os usuários possam editar mesmo quando estiverem offline.

## <a name="review-core-data"></a>Examinar o modelo de dados principal
Ao usar o armazenamento offline de dados principais, você deve definir tabelas e campos específicos em seu modelo de dados. O aplicativo de exemplo já inclui um modelo de dados com o formato correto. Nesta seção, percorreremos essas tabelas para mostrar como elas são usadas.

Abra **QSDataModel. xcdatamodeld**. Quatro tabelas são definidas – três que são usadas pelo SDK e uma que é usada para os próprios itens de tarefas:
  * MS_TableOperations: rastreia os itens que precisam ser sincronizados com o servidor.
  * MS_TableOperationErrors: rastreia os erros que ocorrem durante a sincronização offline.
  * MS_TableConfig: controla a hora da última atualização para a última operação de sincronização para todas as operações de pull.
  * TodoItem: armazena os itens de tarefas pendentes. As colunas do sistema **createdAt**, **updatedAt**e **version** são propriedades opcionais do sistema.

> [!NOTE]
> O SDK de aplicativos móveis reserva nomes de coluna que começam com " **``** ". Não use esse prefixo com nada além das colunas do sistema. Caso contrário, os nomes de coluna serão modificados quando você usar o back-end remoto.
>
>

Ao usar o recurso de sincronização offline, defina as três tabelas do sistema e a tabela de dados.

### <a name="system-tables"></a>Tabelas de sistema

**MS_TableOperations**  

![Atributos da tabela MS_TableOperations][defining-core-data-tableoperations-entity]

| Atributo | Tipo |
| --- | --- |
| ID | Inteiro 64 |
| itemId | String |
| propriedades | Dados binários |
| Tabela | String |
| tableKind | Inteiro 16 |


**MS_TableOperationErrors**

 ![Atributos da tabela MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atributo | Tipo |
| --- | --- |
| ID |String |
| operationId |Inteiro 64 |
| propriedades |Dados binários |
| tableKind |Inteiro 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atributo | Tipo |
| --- | --- |
| ID |String |
| key |String |
| keyType |Inteiro 64 |
| Tabela |String |
| valor |String |

### <a name="data-table"></a>Tabela de dados

**TodoItem**

| Atributo | Tipo | Nota |
| --- | --- | --- |
| ID | Cadeia de caracteres, marcada como obrigatória |Chave primária no repositório remoto |
| concluí | Booleano | Campo de item de tarefas pendentes |
| texto |String |Campo de item de tarefas pendentes |
| CreatedAt | Date | adicional Mapeia para a propriedade do sistema **createdAt** |
| updatedAt | Date | adicional Mapeia para a propriedade do sistema **updatedAt** |
| versão | String | adicional Usado para detectar conflitos, mapeado para a versão |

## <a name="setup-sync"></a>Alterar o comportamento de sincronização do aplicativo
Nesta seção, você modificará o aplicativo para que ele não seja sincronizado na inicialização do aplicativo ou quando você inserir e atualizar itens. Ele é sincronizado somente quando o botão de gesto de atualização é executado.

**Objective-C**:

1. Em **QSTodoListViewController. m**, altere o método **viewDidLoad** para remover a chamada para `[self refresh]` no final do método. Agora, os dados não são sincronizados com o servidor no início do aplicativo. Em vez disso, ele é sincronizado com o conteúdo do repositório local.
2. Em **QSTodoService. m**, modifique a definição de `addItem` para que ela não seja sincronizada depois que o item for inserido. Remova o bloco `self syncData` e substitua-o pelo seguinte:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modifique a definição de `completeItem` conforme mencionado anteriormente. Remova o bloco para `self syncData` e substitua-o pelo seguinte:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

No `viewDidLoad`, em **ToDoTableViewController. Swift**, comente as duas linhas mostradas aqui para parar a sincronização na inicialização do aplicativo. No momento da redação deste artigo, o aplicativo de todo Swift não atualiza o serviço quando alguém adiciona ou conclui um item. Ele atualiza o serviço somente na inicialização do aplicativo.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testar o aplicativo
Nesta seção, você se conecta a uma URL inválida para simular um cenário offline. Quando você adiciona itens de dados, eles são mantidos no armazenamento de dados principal local, mas não são sincronizados com o back-end do aplicativo móvel.

1. Altere a URL do aplicativo móvel em **QSTodoService. m** para uma URL inválida e execute o aplicativo novamente:

   **Objective-C**. Em QSTodoService. m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. Em ToDoTableViewController. Swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adicione alguns itens pendentes. Encerre o simulador (ou feche o aplicativo de maneira forçada) e reinicie-o. Verifique se as alterações persistem.

3. Exiba o conteúdo da tabela **TodoItem** remota:
   * Para um back-end do node. js, vá para a [portal do Azure](https://portal.azure.com/) e, em seu back-end de aplicativo móvel, clique em **tabelas fáceis** > **TodoItem**.  
   * Para um back-end do .NET, use uma ferramenta SQL, como SQL Server Management Studio ou um cliente REST, como o Fiddler ou o postmaster.  

4. Verifique se os novos itens *não* foram sincronizados com o servidor.

5. Altere a URL de volta para a correta em **QSTodoService. m**e execute o aplicativo novamente.

6. Execute o gesto de atualização puxando a lista de itens para baixo.  
Um controle giratório de progresso é exibido.

7. Exiba os dados de **TodoItem** novamente. Os itens de tarefas novos e alterados agora devem ser exibidos.

## <a name="summary"></a>Resumo
Para dar suporte ao recurso de sincronização offline, usamos a interface `MSSyncTable` e inicializamos `MSClient.syncContext` com um repositório local. Nesse caso, o armazenamento local era um banco de dados baseado em data base.

Ao usar um repositório local de dados principais, você deve definir várias tabelas com as [Propriedades corretas do sistema](#review-core-data).

As operações CRUD (criar, ler, atualizar e excluir) normais para aplicativos móveis funcionam como se o aplicativo ainda estiver conectado, mas todas as operações ocorrerão no repositório local.

Quando sincronizamos o repositório local com o servidor, usamos o método **MSSyncTable. pullWithQuery** .

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de dados offline em aplicativos móveis]
* [Cobertura de nuvem: sincronização offline nos serviços móveis do Azure] \(a vídeo é sobre os serviços móveis, mas a sincronização offline de aplicativos móveis funciona de maneira semelhante. \)

<!-- URLs. -->


[Criar um aplicativo iOS]: app-service-mobile-ios-get-started.md
[Sincronização de dados offline em aplicativos móveis]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cobertura de nuvem: sincronização offline nos serviços móveis do Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
