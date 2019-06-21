---
title: Sincronização de dados offline em aplicações móveis do Azure | Documentos da Microsoft
description: Referência conceptual e descrição geral da funcionalidade de sincronização de dados offline para aplicações móveis do Azure
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205094"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de Dados Offline nas Aplicações Móveis do Azure
## <a name="what-is-offline-data-sync"></a>O que é a sincronização de dados offline?
Sincronização de dados offline é um cliente e servidor funcionalidade SDK do Azure Mobile Apps que torna mais fácil para os programadores criarem aplicações que estão funcionais sem uma ligação de rede.

Quando a aplicação está no modo offline, pode ainda criar e modificar dados, que são salvas num armazenamento local. Quando a aplicação esteja novamente online, ele pode sincronizar alterações locais feitas com o back-end de aplicação móvel do Azure. A funcionalidade também inclui suporte para a detecção de conflitos quando o mesmo registo é alterado no cliente e o back-end. Em seguida, podem ser processados conflitos no servidor ou o cliente.

Sincronização offline apresenta várias vantagens:

* Melhorar a capacidade de resposta da aplicação ao colocar em cache os dados de servidor localmente no dispositivo
* Crie aplicações robustas que permanecem úteis quando surgem problemas de rede
* Permitir que os utilizadores finais criar e alterar dados mesmo quando não é possível aceder de rede, suporte a cenários com pouca ou nenhuma conectividade
* Sincronizar dados em vários dispositivos e detectar conflitos quando o mesmo registo é modificado por dois dispositivos
* Limitar a utilização de rede em redes de alta latência ou com tráfego limitado

Os tutoriais seguintes mostram como adicionar sincronização offline para os seus clientes móveis com aplicações móveis do Azure:

* [Android: Permitir sincronização offline]
* [Apache Cordova: Permitir sincronização offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Permitir sincronização offline]
* [Xamarin iOS: Permitir sincronização offline]
* [Xamarin Android: Permitir sincronização offline]
* [Xamarin.Forms: Permitir sincronização offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma universal do Windows: Permitir sincronização offline]

## <a name="what-is-a-sync-table"></a>O que é uma tabela de sincronização?
Para aceder ao ponto de final de "/ tabelas", os SDKs do cliente do Azure Mobile fornecem interfaces, como `IMobileServiceTable` (cliente SDK do .NET) ou `MSTable` (cliente iOS). Estas APIs ligam diretamente para o back-end de aplicação móvel do Azure e falharem se o dispositivo de cliente não tem uma ligação de rede.

Para suportar a utilização offline, a sua aplicação em vez disso, deve utilizar o *tabela de sincronização* APIs, como `IMobileServiceSyncTable` (cliente SDK do .NET) ou `MSSyncTable` (cliente iOS). Todas as mesmas operações de CRUD (Create, Read, Update, Delete) trabalham em relação a sincronização de APIs de tabela, exceto agora ler a partir ou escrever para um *arquivo local*. Antes de quaisquer operações de tabela de sincronização podem ser realizadas, o arquivo local tem de ser inicializado.

## <a name="what-is-a-local-store"></a>O que é um armazenamento local?
Um armazenamento local é a camada de persistência de dados no dispositivo cliente. Os SDKs de cliente de Mobile Apps do Azure fornecem uma implementação de arquivo local padrão. No Windows, Xamarin e Android, baseia-se no SQLite. No iOS, baseia-se em dados principais.

Para utilizar a implementação com base em SQLite num Windows Phone ou Microsoft Store, terá de instalar uma extensão do SQLite. Para obter mais informações, consulte [plataforma Universal do Windows: Permitir sincronização offline]. Android e iOS fornecido com uma versão do SQLite no sistema operativo do dispositivo em si, pelo que não é necessário fazer referência a sua própria versão do SQLite.

Os desenvolvedores também podem implementar seu próprio arquivo local. Por exemplo, se desejar armazenar os dados num formato encriptado no cliente móvel, pode definir um arquivo local que utiliza SQLCipher para encriptação.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?
R *contexto de sincronização* está associado um objeto de cliente móvel (tal como `IMobileServiceClient` ou `MSClient`) e regista as alterações efetuadas com tabelas de sincronização. O contexto de sincronização mantém uma *fila de operação*, que mantém uma lista ordenada de operações CUD (Create, Update, Delete) que é enviada mais tarde para o servidor.

Um arquivo local está associado com o contexto de sincronização com como um método initialize `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK de cliente .NET].

## <a name="how-sync-works"></a>Funciona como offline de sincronização
Ao usar tabelas de sincronização, o código do cliente controla quando as alterações locais sejam sincronizadas com um back-end de aplicação móvel do Azure. Nada é enviado para o back-end até que haja uma chamada para *push* alterações locais feitas. Da mesma forma, o armazenamento local é preenchido com novos dados apenas quando existe uma chamada para *pull* dados.

* **Push**: Push é uma operação no contexto de sincronização e envia todas as alterações CUD desde o último push. Tenha em atenção que não é possível enviar apenas alterações de uma tabela individual, uma vez que caso contrário, operações poderiam ser enviadas fora de ordem. Push executa uma série de chamadas REST para o back-end aplicação móvel do Azure, que por sua vez modifica sua base de dados do servidor.
* **Extrair**: Extração é executada numa base por tabela e pode ser personalizada com uma consulta para recuperar apenas um subconjunto dos dados de servidor. Os SDKs de cliente móveis do Azure, em seguida, insira os dados resultantes para o arquivo local.
* **Pushes implícitas**: Se uma solicitação for executada com base numa tabela que tenha locais atualizações pendentes, a solicitação primeiro executa um `push()` no contexto de sincronização. Este push ajuda a minimizar os conflitos entre as alterações que já estão a ser colocados em fila e os novos dados do servidor.
* **Sincronização incremental**: o primeiro parâmetro para a operação de solicitação é um *nome da consulta* que é utilizado apenas no cliente. Se utilizar um nome de consulta não nulo, o SDK do Mobile Azure realiza uma *sincronização incremental*. Sempre que uma operação de solicitação retorna um conjunto de resultados, o mais `updatedAt` timestamp desse conjunto de resultados é armazenado nas tabelas de sistema local do SDK. Operações de recebimento subsequentes obtêm apenas os registos após esse timestamp.

  Utilizar a sincronização incremental, o servidor tem de devolver significativo `updatedAt` valores e também tem de suportar ordenar por este campo. No entanto, uma vez que o SDK adiciona seu próprio tipo no campo updatedAt, não é possível utilizar uma consulta de extração que tem o seu próprio `orderBy` cláusula.

  O nome da consulta pode ser qualquer cadeia de caracteres que escolher, mas tem de ser exclusivo para cada consulta lógica na sua aplicação.
  Caso contrário, as operações de recebimento diferente poderiam substituir o mesmo carimbo de hora de sincronização incremental e suas consultas podem retornar resultados incorretos.

  Se a consulta tem um parâmetro, é uma forma de criar um nome exclusivo de consulta incorporar o valor do parâmetro.
  Por exemplo, se estiver a filtrar no ID de utilizador, o nome da sua consulta seria da seguinte forma (em c#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se pretender desativar a sincronização incremental, passar `null` como o ID de consulta. Neste caso, todos os registos são obtidos em cada chamada para `PullAsync`, que é potencialmente ineficiente.
* **Remover**: Pode limpar o conteúdo do arquivo local com `IMobileServiceSyncTable.PurgeAsync`.
  Remoção pode ser necessário se o ter dados obsoletos da base de dados do cliente, ou se pretender eliminar todas as alterações pendentes.

  Uma remoção limpa uma tabela do arquivo local. Se existirem operações que aguardam a sincronização com a base de dados do servidor, a remoção lança uma exceção a menos que o *Forçar remoção* parâmetro estiver definido.

  Como um exemplo de dados obsoletos no cliente, suponha que o exemplo de "lista de tarefas", Device1 obtém apenas os itens que não estão concluídos. Um todoitem "Comprar milk" está marcada como concluído no servidor, por outro dispositivo. No entanto, Device1 ainda tem o todoitem "Comprar milk" no arquivo local porque ele só está obtendo os itens que não são marcados como concluídos. Uma remoção limpa este item obsoleto.

## <a name="next-steps"></a>Passos Seguintes
* [iOS: Permitir sincronização offline]
* [Xamarin iOS: Permitir sincronização offline]
* [Xamarin Android: Permitir sincronização offline]
* [Plataforma universal do Windows: Permitir sincronização offline]

<!-- Links -->
[SDK de cliente .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Permitir sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Permitir sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Permitir sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Permitir sincronização offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Plataforma universal do Windows: Permitir sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
