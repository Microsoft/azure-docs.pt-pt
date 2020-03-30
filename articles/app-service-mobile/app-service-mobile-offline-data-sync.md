---
title: Sincronização de dados offline
description: Referência conceptual e visão geral da funcionalidade de sincronização de dados offline para aplicações móveis Azure
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458754"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de Dados Offline nas Aplicações Móveis do Azure

## <a name="what-is-offline-data-sync"></a>O que é sincronização de dados offline?
Offline data sync é uma funcionalidade SDK cliente e servidor de Aplicações Móveis Azure que facilita a criação de apps que são funcionais sem uma ligação de rede.

Quando a sua aplicação está em modo offline, ainda pode criar e modificar dados, que são guardados numa loja local. Quando a aplicação estiver novamente online, pode sincronizar as alterações locais com o seu backend da App Móvel Azure. A funcionalidade também inclui suporte para detetar conflitos quando o mesmo registo é alterado tanto no cliente como no backend. Os conflitos podem então ser tratados no servidor ou no cliente.

A sincronização offline tem vários benefícios:

* Melhorar a capacidade de resposta da aplicação ao cortar dados do servidor localmente no dispositivo
* Criar aplicações robustas que permanecem úteis quando há problemas de rede
* Permitir que os utilizadores finais criem e modifiquem dados mesmo quando não há acesso à rede, suportando cenários com pouca ou nenhuma conectividade
* Sync dados em vários dispositivos e deteta conflitos quando o mesmo registo é modificado por dois dispositivos
* Limitar a utilização da rede em redes de alta latência ou medid

Os seguintes tutoriais mostram como adicionar sincronização offline aos seus clientes móveis usando aplicações móveis Azure:

* [Android: Ativar sincronização offline]
* [Apache Cordova: Ativar sincronização offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Ativar sincronização offline]
* [Xamarin iOS: Ativar sincronização offline]
* [Xamarin Android: Ativar sincronização offline]
* [Xamarin.Forms: Ativar sincronização offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma Universal Windows: Ativar sincronização offline]

## <a name="what-is-a-sync-table"></a>O que é uma mesa de sincronização?
Para aceder ao ponto final "/tabelas", os SDKs do `IMobileServiceTable` cliente Azure Mobile fornecem `MSTable` interfaces como (.CLIENTE NET SDK) ou (cliente iOS). Estas APIs ligam-se diretamente ao backend da App Móvel Azure e falham se o dispositivo cliente não tiver uma ligação de rede.

Para suportar a utilização offline, a sua aplicação deve, em vez disso, utilizar as APIs da *tabela de sincronização,* tais como `IMobileServiceSyncTable` (.CLIENTE NET SDK) ou `MSSyncTable` (cliente iOS). Todas as mesmas operações crud (Criar, Ler, Atualizar, Excluir) funcionam contra APIs da tabela de sincronização, exceto que agora lêem ou escrevem para uma *loja local*. Antes de serem realizadas quaisquer operações de sincronização da tabela, a loja local deve ser inicializada.

## <a name="what-is-a-local-store"></a>O que é uma loja local?
Uma loja local é a camada de persistência de dados no dispositivo cliente. Os SDKs clientes de Aplicações Móveis Azure fornecem uma implementação padrão da loja local. No Windows, Xamarin e Android, é baseado na SQLite. No iOS, baseia-se em Dados Fundamentais.

Para utilizar a implementação baseada em SQLite no Windows Phone ou microsoft Store, é necessário instalar uma extensão SQLite. Para mais informações, consulte [universal Windows Platform: Enable offline sync]. Android e iOS com uma versão do SQLite no próprio sistema operativo do dispositivo, pelo que não é necessário fazer referência à sua própria versão do SQLite.

Os desenvolvedores também podem implementar a sua própria loja local. Por exemplo, se desejar armazenar dados num formato encriptado no cliente móvel, pode definir uma loja local que utiliza o SQLCipher para encriptação.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?
Um *contexto de sincronização* está associado a `IMobileServiceClient` `MSClient`um objeto de cliente móvel (como ou) e rastreia alterações que são feitas com tabelas de sincronização. O contexto de sincronização mantém uma fila de *funcionamento*, que mantém uma lista ordenada de operações cud (Criar, Atualizar, Excluir) que é posteriormente enviada para o servidor.

Uma loja local está associada ao contexto de sincronização utilizando um método inicializado como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK]do cliente .NET .

## <a name="how-offline-synchronization-works"></a><a name="how-sync-works"></a>Como funciona a sincronização offline
Ao utilizar tabelas de sincronização, o código do cliente controla quando as alterações locais são sincronizadas com um backend da App Móvel Azure. Nada é enviado para o backend até que haja uma chamada para *impulsionar* as mudanças locais. Da mesma forma, a loja local é povoada com novos dados apenas quando há uma chamada para *puxar* dados.

* **Push**: Push é uma operação no contexto de sincronização e envia todas as alterações de CUD desde o último empurrão. Note que não é possível enviar apenas alterações de uma tabela individual, porque caso contrário as operações poderiam ser enviadas fora de ordem. Push executa uma série de chamadas REST para o seu backend da App Móvel Azure, que por sua vez modifica a base de dados do servidor.
* **Pull**: Pull é realizado numa base por mesa e pode ser personalizado com uma consulta para recuperar apenas um subconjunto dos dados do servidor. Os SDKs do cliente Azure Mobile inserem os dados resultantes na loja local.
* **Empurrões Implícitos**: Se um puxão for executado contra uma tabela que `push()` tem atualizações locais pendentes, o pull executa primeiro um no contexto de sincronização. Este impulso ajuda a minimizar os conflitos entre alterações que já estão em fila e novos dados do servidor.
* **Incremental Sync**: o primeiro parâmetro para a operação de puxar é um *nome de consulta* que é usado apenas no cliente. Se utilizar um nome de consulta não nulo, o Azure Mobile SDK executa uma *sincronização incremental*. Cada vez que uma operação de pull `updatedAt` devolve um conjunto de resultados, o mais recente carimbo de tempo desse conjunto de resultados é armazenado nas tabelas do sistema local SDK. As operações subsequentes de puxar só recuperam registos após o carimbo temporal.

  Para utilizar sincronização incremental, `updatedAt` o servidor deve devolver valores significativos e também suportar a triagem por este campo. No entanto, uma vez que o SDK adiciona o seu próprio tipo `orderBy` no campo actualizadoAt, não pode utilizar uma consulta de pull que tem a sua própria cláusula.

  O nome de consulta pode ser qualquer corda que escolha, mas deve ser único para cada consulta lógica na sua app.
  Caso contrário, diferentes operações de pull podem substituir o mesmo carimbo de tempo de sincronização incremental e as suas consultas podem devolver resultados incorretos.

  Se a consulta tiver um parâmetro, uma maneira de criar um nome único de consulta é incorporar o valor do parâmetro.
  Por exemplo, se estiver a filtrar o uso, o seu nome de consulta pode ser o seguinte (em C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se quiser optar por não sincronizar incrementalmente, passe `null` como id de consulta. Neste caso, todos os registos são `PullAsync`recuperados em todas as chamadas, o que é potencialmente ineficiente.
* **Purgação**: Pode limpar o conteúdo `IMobileServiceSyncTable.PurgeAsync`da loja local utilizando .
  A purga pode ser necessária se tiver dados na base de dados do cliente ou se pretender descartar todas as alterações pendentes.

  Uma purga limpa uma mesa da loja local. Se houver operações à espera de sincronização com a base de dados do servidor, a purga lança uma exceção a menos que o parâmetro de purga de *força* esteja definido.

  Como exemplo de dados sobre o cliente, suponha no exemplo da "lista de todo", o Dispositivo1 apenas puxa itens que não estejam concluídos. Um todoitem "Comprar leite" é marcado no servidor por outro dispositivo. No entanto, o Dispositivo1 ainda tem o todoitem "Comprar leite" na loja local porque está apenas a puxar itens que não estão marcados completos. Uma purga limpa este item velho.

## <a name="next-steps"></a>Passos seguintes
* [iOS: Ativar sincronização offline]
* [Xamarin iOS: Ativar sincronização offline]
* [Xamarin Android: Ativar sincronização offline]
* [Plataforma Universal Windows: Ativar sincronização offline]

<!-- Links -->
[.NET cliente SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Ativar sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Ativar sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Ativar sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Ativar sincronização offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Plataforma Universal Windows: Ativar sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
