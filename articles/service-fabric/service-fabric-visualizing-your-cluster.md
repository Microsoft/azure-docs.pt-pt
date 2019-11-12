---
title: Visualizando seu cluster usando o Azure Service Fabric Explorer | Microsoft Docs
description: Service Fabric Explorer é um aplicativo para inspecionar e gerenciar aplicativos de nuvem e nós em um cluster Microsoft Azure Service Fabric.
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
ms.openlocfilehash: 9b873b5a68979b8225c44c32e0b9494408e35ac1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927185"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizar o cluster com o Service Fabric Explorer

Service Fabric Explorer (SFX) é uma ferramenta de código-fonte aberto para inspecionar e gerenciar clusters de Service Fabric do Azure. Service Fabric Explorer é um aplicativo de área de trabalho para Windows, macOS e Linux.

## <a name="service-fabric-explorer-download"></a>Download de Service Fabric Explorer

Use os links a seguir para baixar o Service Fabric Explorer como um aplicativo de área de trabalho:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A versão da área de trabalho do Service Fabric Explorer pode ter mais ou menos recursos do que o suporte ao cluster. Você pode fazer fallback para a versão Service Fabric Explorer implantada no cluster para garantir a compatibilidade total de recursos.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Executando Service Fabric Explorer do cluster

Service Fabric Explorer também é hospedado em um ponto de extremidade de gerenciamento HTTP do Cluster Service Fabric. Para iniciar o SFX em um navegador da Web, navegue até o ponto de extremidade de gerenciamento HTTP do cluster em qualquer navegador, por exemplo, https:\//clusterFQDN: 19080.

Para a instalação da estação de trabalho do desenvolvedor, você pode iniciar Service Fabric Explorer no cluster local navegando até https://localhost:19080/Explorer. Veja este artigo para [preparar seu ambiente de desenvolvimento](service-fabric-get-started.md).

> [!NOTE]
> Se o cluster estiver protegido por um certificado autoassinado, você receberá uma mensagem de erro do navegador da Web "este site não é seguro". Você pode simplesmente prosseguir com os navegadores da Web mais modernos, substituindo o aviso. Em um ambiente de produção, seu cluster deve ser protegido usando o nome comum e um certificado emitido por uma autoridade de certificação. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Conectar-se a um Cluster Service Fabric
Para se conectar a um Cluster Service Fabric, você precisa do ponto de extremidade de gerenciamento de clusters (FQDN/IP) e da porta do ponto de extremidade de gerenciamento HTTP (19080 por padrão). Por exemplo, https\://mysfcluster.westus.cloudapp.azure.com:19080. Use a caixa de seleção "conectar-se ao localhost" para se conectar a um cluster local em sua estação de trabalho.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro
Você pode controlar o acesso do cliente ao Cluster Service Fabric com certificados ou usando Azure Active Directory (AAD).

Se você tentar se conectar a um cluster seguro, dependendo da configuração do cluster, será necessário apresentar um certificado de cliente ou entrar usando o AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Entender o layout do Service Fabric Explorer
Você pode navegar pelo Service Fabric Explorer usando a árvore à esquerda. Na raiz da árvore, o painel do cluster fornece uma visão geral do cluster, incluindo um resumo da integridade do aplicativo e do nó.

![Painel do Cluster Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Exibir o layout do cluster
Nós em um Cluster Service Fabric são colocados em uma grade bidimensional de domínios de falha e domínios de atualização. Esse posicionamento garante que seus aplicativos permaneçam disponíveis na presença de falhas de hardware e atualizações de aplicativos. Você pode exibir como o cluster atual é apresentado usando o mapa de cluster.

![Service Fabric Explorer o mapa de cluster][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Exibir aplicativos e serviços
O cluster contém duas subárvores: uma para aplicativos e outra para nós.

Você pode usar a exibição de aplicativo para navegar pela hierarquia lógica de Service Fabric: aplicativos, serviços, partições e réplicas.

No exemplo a seguir, o aplicativo **MyApp** consiste em dois serviços, **MyStatefulService** e **WebService**. Como **MyStatefulService** tem monitoração de estado, ele inclui uma partição com uma réplica primária e duas secundárias. Por outro lado, WebSvcService é sem monitoração de estado e contém uma única instância.

![Service Fabric Explorer exibição do aplicativo][sfx-application-tree]

Em cada nível da árvore, o painel principal mostra informações pertinentes sobre o item. Por exemplo, você pode ver o status de integridade e a versão de um serviço específico.

![Painel do Service Fabric Explorer Essentials][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Exibir os nós do cluster
A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó. Mais especificamente, você pode ver quais réplicas estão sendo executadas nesse momento.

## <a name="actions"></a>Ações
O Service Fabric Explorer oferece uma maneira rápida de invocar ações em nós, aplicativos e serviços no cluster.

Por exemplo, para excluir uma instância do aplicativo, escolha o aplicativo na árvore à esquerda e, em seguida, escolha **ações** > **excluir aplicativo**.

![Excluindo um aplicativo no Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Você pode executar as mesmas ações clicando nas reticências ao lado de cada elemento.
>
> Todas as ações que podem ser executadas por meio de Service Fabric Explorer também podem ser executadas por meio do PowerShell ou de uma API REST, para habilitar a automação.
>
>

Você também pode usar Service Fabric Explorer para criar instâncias de aplicativo para um determinado tipo de aplicativo e versão. Escolha o tipo de aplicativo no modo de exibição de árvore e clique no link **criar instância de aplicativo** ao lado da versão que você deseja no painel direito.

![Criando uma instância do aplicativo no Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer não dá suporte a parâmetros ao criar instâncias de aplicativo. As instâncias de aplicativo usam valores de parâmetro padrão.
>
>

## <a name="event-store"></a>Repositório de eventos
O EventStore é um recurso oferecido pela plataforma que fornece Service Fabric eventos de plataforma disponíveis no Service Fabric Explorer e por meio da API REST. Você pode ver uma exibição de instantâneo do que está acontecendo no cluster para cada entidade, por exemplo, nó, serviço, aplicativo e consulta com base na hora do evento. Você também pode ler mais sobre o EventStore na [visão geral do EventStore](service-fabric-diagnostics-eventstore.md).   

![EventStore][sfx-eventstore]

>[!NOTE]
>A partir da versão Service Fabric 6,4. EventStore não está habilitado por padrão e deve ser habilitado no modelo do Resource Manager

>[!NOTE]
>A partir da versão Service Fabric 6,4. as APIs EventStore só estão disponíveis para clusters do Windows em execução somente no Azure. Estamos trabalhando para portar essa funcionalidade para o Linux, bem como para nossos clusters autônomos.

## <a name="image-store-viewer"></a>Visualizador de Repositório de Imagens
O Visualizador de repositório de imagens é um recurso oferecido se o uso de Repositório de Imagens nativo permitir a exibição do conteúdo atual do repositório de imagens e a obtenção de informações de arquivos e pastas, juntamente com a remoção de arquivos/pastas.

![Service Fabric Explorer o mapa de cluster][sfx-imagestore]

## <a name="backup-and-restore"></a>Cópia de Segurança e Restauro
O Service Fabric Explorer oferece a capacidade de fazer a interface com [backup e restauração](./service-fabric-reliable-services-backup-restore.md). Para ver os recursos de backup e restauração no SFX, o modo avançado deve ser habilitado.

![Habilitar o modo avançado][0]
 
As seguintes operações são possíveis:

* Criar, editar e excluir uma política de backup.
* Habilitar e desabilitar o backup de um aplicativo, serviço ou partição.
* Suspender e retomar o backup de um aplicativo, serviço ou partição.
* Disparar e acompanhar o backup de uma partição.
* Disparar e rastrear a restauração de uma partição.

Para obter mais informações sobre o serviço de backup e restauração, consulte a [referência da API REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Passos seguintes
* [Gerenciando seus aplicativos Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric a implantação de aplicativo usando o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png