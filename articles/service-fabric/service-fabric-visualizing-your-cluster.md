---
title: Visualizar o seu cluster usando o Azure Service Fabric Explorer
description: Service Fabric Explorer é uma aplicação para inspecionar e gerir aplicações e nós em nuvem num cluster microsoft Azure Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258190"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizar o cluster com o Service Fabric Explorer

Service Fabric Explorer (SFX) é uma ferramenta de código aberto para inspecionar e gerir clusters de tecido de serviço Azure. Service Fabric Explorer é uma aplicação de desktop para Windows, macOS e Linux.

## <a name="service-fabric-explorer-download"></a>Download do Explorador de Tecido de Serviço

Utilize os seguintes links para baixar o Service Fabric Explorer como uma aplicação de ambiente de trabalho:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A versão de desktop do Service Fabric Explorer pode ter mais ou menos funcionalidades do que o suporte do cluster. Pode voltar à versão Service Fabric Explorer implantada no cluster para garantir a compatibilidade total da funcionalidade.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Running Service Fabric Explorer do cluster

O Service Fabric Explorer também está hospedado num ponto final de gestão http do cluster service Fabric. Para lançar o SFX num navegador web, navegue para o ponto final de\/gestão http do cluster a partir de qualquer navegador - por exemplo https: /clusterFQDN:19080.

Para a configuração da estação de trabalho do desenvolvedor, https://localhost:19080/Explorerpode lançar o Service Fabric Explorer no seu cluster local navegando até . Veja este artigo para preparar o seu ambiente de [desenvolvimento.](service-fabric-get-started.md)

> [!NOTE]
> Se o seu cluster for protegido por um certificado auto-assinado, receberá uma mensagem de erro do navegador "Este site não é seguro". Você pode simplesmente proceder através da maioria dos navegadores web modernos, substituindo o aviso. Num ambiente de produção, o seu cluster deve ser protegido com o nome comum e um certificado emitido pela autoridade do certificado. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Ligar a um cluster do Service Fabric
Para ligar a um cluster de tecido de serviço, necessita do ponto final de gestão de clusters (FQDN/IP) e da porta final de gestão HTTP (19080 por padrão). Por exemplo\:https //mysfcluster.westus.cloudapp.azure.com:19080. Utilize a caixa de verificação "Connect to localhost" para se ligar a um cluster local na sua estação de trabalho.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro
Pode controlar o acesso do cliente ao seu cluster de Tecido de Serviço, quer com certificados, quer utilizando o Azure Ative Directory (AAD).

Se tentar ligar-se a um cluster seguro, dependendo da configuração do cluster será necessário apresentar um certificado de cliente ou assinar através do AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Compreenda o layout do Explorador de Tecido de Serviço
Pode navegar através do Service Fabric Explorer utilizando a árvore à esquerda. Na raiz da árvore, o painel de instrumentos do cluster fornece uma visão geral do seu cluster, incluindo um resumo da aplicação e da saúde do nó.

![Painel de cluster do Explorador de Tecido de Serviço][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Veja o layout do cluster
Os nós de um cluster de tecido de serviço são colocados através de uma grelha bidimensional de domínios de falha e domínios de upgrade. Esta colocação garante que as suas aplicações permanecem disponíveis na presença de falhas de hardware e atualizações de aplicações. Pode ver como o cluster atual é estabelecido usando o mapa do cluster.

![Mapa do cluster do Explorador de Tecido de Serviço][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Ver aplicações e serviços
O aglomerado contém duas subárvores: uma para aplicações e outra para nós.

Pode utilizar a visão de aplicação para navegar através da hierarquia lógica do Service Fabric: aplicações, serviços, divisórias e réplicas.

No exemplo abaixo, a aplicação **MyApp** é composta por dois serviços, **MyStatefulService** e **WebService.** Uma vez que **o MyStatefulService** é imponente, inclui uma partição com uma réplica primária e duas secundárias. Em contraste, o WebSvcService é apátrida e contém uma única instância.

![Vista de aplicação do Explorador de Tecido de Serviço][sfx-application-tree]

Em cada nível da árvore, o painel principal mostra informações pertinentes sobre o item. Por exemplo, pode ver o estado de saúde e a versão para um determinado serviço.

![Painel essencial do Explorador de Tecidode Serviço][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Veja os nós do cluster
A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó. Mais especificamente, pode ver quais as réplicas que estão a correr por lá.

## <a name="actions"></a>Ações
O Service Fabric Explorer oferece uma forma rápida de invocar ações em nós, aplicações e serviços dentro do seu cluster.

Por exemplo, para eliminar uma instância de aplicação, escolha a aplicação a partir da árvore à esquerda e, em seguida, escolha **Ações** > **Apagar Aplicação**.

![Apagar uma aplicação no Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Pode realizar as mesmas ações clicando na elipse ao lado de cada elemento.
>
> Todas as ações que podem ser realizadas através do Service Fabric Explorer também podem ser realizadas através da PowerShell ou de uma API REST, para permitir a automatização.
>
>

Também pode utilizar o Service Fabric Explorer para criar instâncias de aplicação para um determinado tipo e versão de aplicação. Escolha o tipo de aplicação na vista da árvore e, em seguida, clique no link de instância da **aplicação Create** ao lado da versão que deseja no painel certo.

![Criar uma instância de aplicação no Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> O Service Fabric Explorer não suporta parâmetros na criação de instâncias de aplicação. As instâncias de aplicação utilizam valores de parâmetros predefinidos.
>
>

## <a name="event-store"></a>Loja de Eventos
EventStore é uma funcionalidade oferecida pela plataforma que fornece eventos da plataforma Service Fabric disponíveis no Service Fabric Explorer e através da REST API. Pode ver uma visão instantânea do que se passa no seu cluster para cada entidade, por exemplo, nó, serviço, aplicação e consulta com base no tempo do evento. Também pode ler mais sobre a EventStore na visão geral da [EventStore](service-fabric-diagnostics-eventstore.md).   

![Loja de Eventos][sfx-eventstore]

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. EventStore não está ativada por padrão e deve ser ativada no modelo de gestor de recursos

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. as APIs da EventStore só estão disponíveis para clusters Windows que executam apenas em Azure. Estamos a trabalhar na colocação desta funcionalidade para o Linux, bem como para os nossos clusters autónomos.

## <a name="image-store-viewer"></a>Espectador de loja de imagem
O visualizador da loja de imagens é uma funcionalidade oferecida se utilizar a Native Image Store que permite visualizar o conteúdo atual da loja Image e obter informações de ficheiros e pastas, juntamente com a remoção de ficheiros/pastas.

![Mapa do cluster do Explorador de Tecido de Serviço][sfx-imagestore]

## <a name="backup-and-restore"></a>Cópia de Segurança e Restauro
Service Fabric Explorer oferece a capacidade de interagir com [backup e restauro](./service-fabric-reliable-services-backup-restore.md). Para ver as funcionalidades de Backup e Restauro no SFX, o modo avançado deve ser ativado.

![Ativar o modo avançado][0]
 
São possíveis as seguintes operações:

* Criar, editar e eliminar uma Política de Backup.
* Ativar e desativar o Backup para uma aplicação, serviço ou partição.
* Suspender e retomar o Backup para uma aplicação, serviço ou partição.
* Gatilho e rastreie o backup de uma divisória.
* Gatilho e faixa Restaurar para uma partição.

Para mais informações sobre o serviço de Backup e Restauro, consulte a [referência REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Passos seguintes
* [Gerir as suas aplicações de Tecido de Serviço no Estúdio Visual](service-fabric-manage-application-in-visual-studio.md)
* [Implementação de aplicações de tecido de serviço utilizando powerShell](service-fabric-deploy-remove-applications.md)

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