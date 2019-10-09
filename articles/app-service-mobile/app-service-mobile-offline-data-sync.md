---
title: Sincronização de dados offline em aplicativos móveis do Azure | Microsoft Docs
description: Referência conceitual e visão geral do recurso de sincronização de dados offline para aplicativos móveis do Azure
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
ms.openlocfilehash: 7f5b24915ddb9fd12085583844770dd7587f2394
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025217"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de Dados Offline nas Aplicações Móveis do Azure

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="what-is-offline-data-sync"></a>O que é a sincronização de dados offline?
A sincronização de dados offline é um recurso de cliente e SDK de servidor de aplicativos móveis do Azure que facilita para os desenvolvedores criar aplicativos que são funcionais sem uma conexão de rede.

Quando seu aplicativo estiver no modo offline, você ainda poderá criar e modificar dados, que são salvos em um repositório local. Quando o aplicativo estiver online novamente, ele poderá sincronizar as alterações locais com o back-end do aplicativo móvel do Azure. O recurso também inclui suporte para a detecção de conflitos quando o mesmo registro é alterado no cliente e no back-end. Em seguida, os conflitos podem ser tratados no servidor ou no cliente.

A sincronização offline tem vários benefícios:

* Melhorar a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Crie aplicativos robustos que permanecem úteis quando há problemas de rede
* Permitir que os usuários finais criem e modifiquem dados mesmo quando não houver acesso à rede, com suporte a cenários com pouca ou nenhuma conectividade
* Sincronizar dados entre vários dispositivos e detectar conflitos quando o mesmo registro é modificado por dois dispositivos
* Limitar o uso de rede em redes de alta latência ou limitadas

Os tutoriais a seguir mostram como adicionar sincronização offline aos seus clientes móveis usando os aplicativos móveis do Azure:

* [Android: Habilitar sincronização offline @ no__t-0
* Cordova @no__t 0Apache: Habilitar sincronização offline @ no__t-0
* [iOS: Habilitar sincronização offline @ no__t-0
* iOS @no__t 0Xamarin: Habilitar sincronização offline @ no__t-0
* Android de @no__t 0Xamarin: Habilitar sincronização offline @ no__t-0
* [Xamarin. Forms: Habilitar sincronização offline @ no__t-0
* Plataforma do Windows @no__t 0Universal: Habilitar sincronização offline @ no__t-0

## <a name="what-is-a-sync-table"></a>O que é uma tabela de sincronização?
Para acessar o ponto de extremidade "/Tables", os SDKs do cliente móvel do Azure fornecem interfaces como `IMobileServiceTable` (SDK do cliente .NET) ou `MSTable` (cliente iOS). Essas APIs se conectam diretamente ao back-end do aplicativo móvel do Azure e falham se o dispositivo cliente não tiver uma conexão de rede.

Para dar suporte ao uso offline, seu aplicativo deve usar as APIs de *tabela de sincronização* , como `IMobileServiceSyncTable` (SDK de cliente .net) ou `MSSyncTable` (cliente Ios). Todas as mesmas operações CRUD (criar, ler, atualizar, excluir) funcionam com as APIs de tabela de sincronização, exceto que agora elas lidam ou gravam em um *repositório local*. Antes que qualquer operação de tabela de sincronização possa ser executada, o repositório local deve ser inicializado.

## <a name="what-is-a-local-store"></a>O que é um armazenamento local?
Um repositório local é a camada de persistência de dados no dispositivo cliente. Os SDKs de cliente dos aplicativos móveis do Azure fornecem uma implementação de armazenamento local padrão. No Windows, Xamarin e Android, ele se baseia no SQLite. No iOS, ele se baseia nos dados principais.

Para usar a implementação baseada em SQLite em Windows Phone ou Microsoft Store, você precisa instalar uma extensão SQLite. Para obter mais informações, consulte plataforma do Windows [Universal: Habilitar sincronização offline @ no__t-0. O Android e o iOS são fornecidos com uma versão do SQLite no próprio sistema operacional do dispositivo, portanto, não é necessário fazer referência à sua própria versão do SQLite.

Os desenvolvedores também podem implementar seu próprio repositório local. Por exemplo, se você quiser armazenar dados em um formato criptografado no cliente móvel, poderá definir um repositório local que usa sqlcipher para criptografia.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?
Um *contexto de sincronização* é associado a um objeto de cliente móvel (como `IMobileServiceClient` ou `MSClient`) e controla as alterações feitas com tabelas de sincronização. O contexto de sincronização mantém uma *fila de operação*, que mantém uma lista ordenada de operações CUD (criar, atualizar, excluir) que é enviada posteriormente ao servidor.

Um repositório local é associado ao contexto de sincronização usando um método Initialize, como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK do cliente .net].

## <a name="how-sync-works"></a>Como funciona a sincronização offline
Ao usar tabelas de sincronização, o código do cliente controla quando as alterações locais são sincronizadas com um back-end do aplicativo móvel do Azure. Nada é enviado para o back-end até que haja uma chamada para *enviar por push* as alterações locais. Da mesma forma, o armazenamento local é populado com novos dados somente quando há uma chamada para dados de *pull* .

* **Enviar por push**: Push é uma operação no contexto de sincronização e envia todas as alterações de CUD desde o último envio por push. Observe que não é possível enviar apenas as alterações de uma tabela individual, pois, caso contrário, as operações podem ser enviadas fora de ordem. O push executa uma série de chamadas REST para o back-end do aplicativo móvel do Azure que, por sua vez, modifica o banco de dados do servidor.
* Efetuar **pull**: O pull é executado em uma base por tabela e pode ser personalizado com uma consulta para recuperar apenas um subconjunto dos dados do servidor. Os SDKs do cliente móvel do Azure inserem os dados resultantes no repositório local.
* **Pushes implícitos**: Se um pull for executado em uma tabela que tenha atualizações locais pendentes, o pull primeiro executará um `push()` no contexto de sincronização. Esse Push ajuda a minimizar os conflitos entre as alterações que já estão na fila e os novos dados do servidor.
* **Sincronização incremental**: o primeiro parâmetro para a operação de pull é um *nome de consulta* que é usado somente no cliente. Se você usar um nome de consulta não nulo, o SDK do Azure Mobile executará uma *sincronização incremental*. Cada vez que uma operação de pull retorna um conjunto de resultados, o carimbo de data/hora `updatedAt` mais recente desse conjunto de resultados é armazenado nas tabelas do sistema local do SDK. As operações de pull subsequentes recuperam somente registros após esse carimbo de data/hora.

  Para usar a sincronização incremental, o servidor deve retornar valores significativos de `updatedAt` e também deve dar suporte à classificação por esse campo. No entanto, como o SDK adiciona sua própria classificação no campo updatedAt, você não pode usar uma consulta pull que tenha sua própria cláusula `orderBy`.

  O nome da consulta pode ser qualquer cadeia de caracteres que você escolher, mas deve ser exclusivo para cada consulta lógica em seu aplicativo.
  Caso contrário, operações de pull diferentes podem substituir o mesmo carimbo de data/hora de sincronização incremental e suas consultas podem retornar resultados incorretos.

  Se a consulta tiver um parâmetro, uma maneira de criar um nome de consulta exclusivo é incorporar o valor do parâmetro.
  Por exemplo, se você estiver filtrando userid, o nome da consulta poderá ser como a seguir C#(em):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se você quiser recusar a sincronização incremental, passe `null` como a ID de consulta. Nesse caso, todos os registros são recuperados em todas as chamadas para `PullAsync`, o que é potencialmente ineficiente.
* **Limpeza**: Você pode limpar o conteúdo do armazenamento local usando `IMobileServiceSyncTable.PurgeAsync`.
  A limpeza poderá ser necessária se você tiver dados obsoletos no banco de dado do cliente ou se desejar descartar todas as alterações pendentes.

  Uma limpeza limpa uma tabela do repositório local. Se houver operações aguardando a sincronização com o banco de dados do servidor, a limpeza lançará uma exceção, a menos que o parâmetro de *limpeza forçada* seja definido.

  Como um exemplo de dados obsoletos no cliente, suponha que no exemplo "lista de tarefas", o Device1 extrai apenas os itens que não foram concluídos. Um todoitem "comprar leite" é marcado como concluído no servidor por outro dispositivo. No entanto, o Device1 ainda tem o todoitem "comprar leite" no armazenamento local porque ele está apenas obtendo itens que não estão marcados como concluídos. Uma limpeza limpa esse item obsoleto.

## <a name="next-steps"></a>Passos seguintes
* [iOS: Habilitar sincronização offline @ no__t-0
* iOS @no__t 0Xamarin: Habilitar sincronização offline @ no__t-0
* Android de @no__t 0Xamarin: Habilitar sincronização offline @ no__t-0
* Plataforma do Windows @no__t 0Universal: Habilitar sincronização offline @ no__t-0

<!-- Links -->
[SDK do cliente .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Habilitar sincronização offline @ no__t-0
[iOS: Habilitar sincronização offline @ no__t-0
iOS @no__t 0Xamarin: Habilitar sincronização offline @ no__t-0
Android de @no__t 0Xamarin: Habilitar sincronização offline @ no__t-0
Plataforma do Windows @no__t 0Universal: Habilitar sincronização offline @ no__t-0
