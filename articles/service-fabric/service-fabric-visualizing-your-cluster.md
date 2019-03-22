---
title: Visualizar o seu cluster com o Azure Service Fabric Explorer | Documentos da Microsoft
description: Service Fabric Explorer é um aplicativo para inspecionar e gerir aplicações na cloud e nós num cluster do Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 47b5e89164173869d6964e2c5e392bb99e0c8555
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317396"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizar o cluster com o Service Fabric Explorer

Service Fabric Explorer (SFX) é uma ferramenta de código-fonte aberto para inspecionar e gerir clusters do Azure Service Fabric. Service Fabric Explorer é um aplicativo de desktop para Windows, macOS e Linux.

## <a name="service-fabric-explorer-download"></a>Download de Service Fabric Explorer

Utilize as seguintes ligações para transferir o Service Fabric Explorer, como um aplicativo de desktop:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A versão de área de trabalho do Service Fabric Explorer pode ter mais ou menos recursos que o suporte de cluster. Pode reverter para a versão de Service Fabric Explorer implementada no cluster para garantir a compatibilidade de todas as funcionalidades.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Service Fabric Explorer em execução do cluster

Service Fabric Explorer também está alojado no ponto final de gestão de HTTP de um cluster Service Fabric. Para iniciar SFX num browser, navegue para o ponto final de gestão de HTTP do cluster a partir de qualquer browser - por exemplo, https:\//clusterFQDN:19080.

Para a configuração de estação de trabalho do desenvolvedor, pode iniciar Service Fabric Explorer no seu cluster local ao navegar para https://localhost:19080/Explorer. Veja este artigo para [preparar o ambiente de desenvolvimento](service-fabric-get-started.md).

> [!NOTE]
> Se o cluster estiver protegido por um certificado autoassinado, que receberá uma mensagem de erro do navegador da web "deste site não é seguro". Simplesmente pode continuar através de browsers mais modernos, substituindo o aviso. Num ambiente de produção do cluster deve ser protegido com o nome comum e um certificado de autoridade emitido de certificado. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Ligar a um cluster do Service Fabric
Para ligar a um cluster do Service Fabric, terá do ponto de final de gestão de clusters (FQDN/IP) e a porta de ponto final de gestão HTTP (19080 por predefinição). Por exemplo, https\:/ / mysfcluster.westus.cloudapp.azure.com:19080. Utilize a caixa de verificação "Ligar ao localhost" para ligar a um cluster local na estação de trabalho.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro
Pode controlar o acesso de cliente para o cluster do Service Fabric com certificados ou utilizar o Azure Active Directory (AAD).

Se tentar ligar a um cluster seguro, em seguida, dependendo da configuração do cluster, terá de apresentar um certificado de cliente ou iniciar sessão com o AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Compreender o esquema de Service Fabric Explorer
Pode navegar através do Service Fabric Explorer, utilizando a árvore à esquerda. Na raiz da árvore, o dashboard do cluster fornece uma visão geral do seu cluster, incluindo um resumo de aplicações e o estado de funcionamento do nó.

![Dashboard de clusters do Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Ver o esquema do cluster
Os nós no cluster do Service Fabric são colocados numa grade bidimensional de domínios de falha e domínios de atualização. Esta colocação garante que as aplicações permanecem disponíveis na presença de falhas de hardware e as atualizações de aplicações. Pode ver como o cluster atual é disposto ao utilizar o mapa de cluster.

![Mapa de cluster do Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Ver aplicações e serviços
O cluster contém dois subárvores: um para aplicativos e outro para nós.

Pode utilizar a vista do aplicativo para navegar pela hierarquia de lógica do Service Fabric: aplicações, serviços, partições e réplicas.

No exemplo abaixo, o aplicativo **MyApp** consiste em dois serviços, **MyStatefulService** e **WebService**. Uma vez que **MyStatefulService** monitoração de estado, ele inclui uma partição com uma primária e duas réplicas secundárias. Por outro lado, WebSvcService é sem monitoração de estado e contém uma única instância.

![Vista de aplicações do Service Fabric Explorer][sfx-application-tree]

Em cada nível da árvore, o painel principal apresenta informações pertinentes sobre o item. Por exemplo, pode ver o estado de funcionamento e a versão para um determinado serviço.

![Painel de essentials do Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Ver os nós do cluster
A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó. Mais especificamente, pode ver que as réplicas estão atualmente em execução lá.

## <a name="actions"></a>Ações
Service Fabric Explorer proporciona uma forma rápida de invocar ações em nós, aplicativos e serviços no seu cluster.

Por exemplo, para eliminar uma instância de aplicação, escolha a aplicação a partir da árvore à esquerda e, em seguida, escolha **ações** > **eliminar aplicação**.

![A eliminar uma aplicação no Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Pode efetuar as mesmas ações ao clicar nas reticências junto a cada elemento.
>
> Também podem ser executada todas as ações que podem ser executadas por meio do Service Fabric Explorer através do PowerShell ou uma API REST, para ativar a automatização.
>
>

Também pode utilizar o Service Fabric Explorer para criar instâncias da aplicação para um tipo de determinado aplicativo e versão. Escolha o tipo de aplicação na vista de árvore, em seguida, clique nas **criar instância de aplicação** ligação junto à versão que gostaria de ter no painel da direita.

![Criar uma instância de aplicação no Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer não suporta parâmetros durante a criação de instâncias da aplicação. Instâncias da aplicação utilizam valores de parâmetro predefinidos.
>
>

## <a name="event-store"></a>Evento Store
EventStore é um recurso oferecido pela plataforma que fornece eventos de plataforma do Service Fabric disponíveis no Service Fabric Explorer e através da REST API. Pode ver uma vista de instantâneo do que está acontecendo no seu cluster para cada entidade por exemplo, nó, serviço, aplicação e a consulta com base na hora do evento. Pode também Saiba mais sobre o EventStore no [descrição geral do EventStore](service-fabric-diagnostics-eventstore.md).   

![EventStore][sfx-eventstore]

>[!NOTE]
>A partir da versão 6.4 do Service Fabric. EventStore não está ativada por predefinição e tem de estar ativada no modelo do resource manager

>[!NOTE]
>A partir da versão 6.4 do Service Fabric. as APIs de EventStore só estão disponíveis para os clusters do Windows em execução no Azure apenas. Estamos a trabalhar no portar essa funcionalidade para Linux, bem como nossos clusters autónomos.


## <a name="next-steps"></a>Passos Seguintes
* [Gestão das suas aplicações do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Implementação de aplicação do Service Fabric com o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
