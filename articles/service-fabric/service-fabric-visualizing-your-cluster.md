---
title: Visualizar o seu cluster usando o Azure Service Fabric Explorer
description: Service Fabric Explorer é uma aplicação para inspecionar e gerir aplicações e nós em nuvem num cluster de tecido de serviço microsoft Azure.
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a45aff305f97610cb2660c2e3f4b4427b905d7d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574060"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizar o cluster com o Service Fabric Explorer

Service Fabric Explorer (SFX) é uma ferramenta de código aberto para inspecionar e gerir clusters de tecidos de serviço Azure. Service Fabric Explorer é uma aplicação para desktop para Windows, macOS e Linux.

## <a name="service-fabric-explorer-download"></a>Download do Service Fabric Explorer

Utilize os seguintes links para descarregar o Service Fabric Explorer como uma aplicação para desktop:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A versão para desktop do Service Fabric Explorer pode ter mais ou menos funcionalidades do que o suporte do cluster. Pode recorrer à versão Service Fabric Explorer implantada no cluster para garantir a compatibilidade total da funcionalidade.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Running Service Fabric Explorer do cluster

O Service Fabric Explorer também está hospedado no ponto final de gestão HTTP de um cluster de tecido de serviço. Para lançar o SFX num navegador web, navegue para o ponto final de gestão HTTP do cluster a partir de qualquer navegador - por exemplo https: \/ /clusterFQDN:19080.

Para a configuração da estação de trabalho do desenvolvedor, pode lançar o Service Fabric Explorer no seu cluster local navegando para https://localhost:19080/Explorer . Veja este artigo para [preparar o seu ambiente de desenvolvimento.](service-fabric-get-started.md)

> [!NOTE]
> Se o seu cluster estiver protegido por um certificado auto-assinado receberá uma mensagem de erro do navegador "Este site não é seguro". Você pode simplesmente prosseguir através da maioria dos navegadores web modernos, sobrenunciando o aviso. Num ambiente de produção, o seu cluster deve ser protegido com um nome comum e um certificado emitido pela autoridade de certificados. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Ligar a um cluster do Service Fabric
Para se ligar a um cluster de Tecido de Serviço, precisa do ponto final de gestão de clusters (FQDN/IP) e da porta de ponta de gestão HTTP (19080 por padrão). Por exemplo https \: //mysfcluster.westus.cloudapp.azure.com:19080. Utilize a caixa de verificação "Connect to localhost" para ligar a um cluster local na sua estação de trabalho.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro
Pode controlar o acesso do cliente ao seu cluster de Tecidos de Serviço com certificados ou utilizando o Azure Ative Directory (AAD).

Se tentar ligar-se a um cluster seguro, então, dependendo da configuração do cluster, será necessário apresentar um certificado de cliente ou iniciar sação através do AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Compreender o layout do Explorador de Tecido de Serviço
Pode navegar através do Service Fabric Explorer utilizando a árvore à esquerda. Na raiz da árvore, o painel de clusters fornece uma visão geral do seu cluster, incluindo um resumo da aplicação e da saúde do nó.

![Painel de cluster do explorador de tecido de serviço][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Veja o layout do cluster
Os nós num cluster de tecido de serviço são colocados através de uma grelha bidimensional de domínios de avaria e domínios de upgrade. Esta colocação garante que as suas aplicações permanecem disponíveis na presença de falhas de hardware e atualizações de aplicações. Pode ver como o cluster atual é definido usando o mapa de cluster.

![Mapa de cluster do Explorador de tecido de serviço][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Ver aplicações e serviços
O cluster contém duas subárvores: uma para aplicações e outra para nós.

Pode utilizar a visão de aplicação para navegar pela hierarquia lógica do Service Fabric: aplicações, serviços, divisórias e réplicas.

No exemplo abaixo, a aplicação **MyApp** é composta por dois serviços, **MyStatefulService** e **WebService.** Uma vez que **o MyStatefulService** é imponente, inclui uma divisória com uma réplica primária e duas réplicas secundárias. Em contraste, o WebSvcService é apátrida e contém uma única instância.

![Vista de aplicação do Explorador de Tecido de Serviço][sfx-application-tree]

Em cada nível da árvore, o painel principal mostra informações pertinentes sobre o item. Por exemplo, pode ver o estado de saúde e a versão para um determinado serviço.

![Painel essencial do Explorador de Tecido de Serviço][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Ver os nosmos do cluster
A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó. Mais especificamente, pode ver quais réplicas estão atualmente a funcionar lá.

## <a name="actions"></a>Ações
O Service Fabric Explorer oferece uma forma rápida de invocar ações sobre nós, aplicações e serviços dentro do seu cluster.

Por exemplo, para eliminar uma instância de aplicação, escolha a aplicação da árvore à esquerda e, em seguida, escolha **Ações**  >  **Eliminar Aplicação**.

![Eliminação de uma aplicação no Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Pode executar as mesmas ações clicando na elipse ao lado de cada elemento.
>
> Todas as ações que podem ser realizadas através do Service Fabric Explorer também podem ser realizadas através do PowerShell ou de uma API REST, para permitir a automatização.
>
>

Também pode utilizar o Service Fabric Explorer para criar instâncias de aplicação para um determinado tipo de aplicação e versão. Escolha o tipo de aplicação na vista da árvore e, em seguida, clique no link de instância de **aplicação Create** ao lado da versão que pretende no painel certo.

![Criar uma instância de aplicação no Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> O Service Fabric Explorer não suporta parâmetros ao criar instâncias de aplicação. As instâncias de aplicação utilizam valores de parâmetros predefinidos.
>
>

## <a name="event-store"></a>Loja de Eventos
O EventStore é uma funcionalidade oferecida pela plataforma que fornece eventos da plataforma Service Fabric disponíveis no Service Fabric Explorer e através da REST API. Pode ver uma visão instantânea do que se passa no seu cluster para cada entidade, por exemplo, nó, serviço, aplicação e consulta com base na hora do evento. Também pode ler mais sobre a Loja de Eventos na Visão Geral da Loja de [Eventos.](service-fabric-diagnostics-eventstore.md)   

![A screenshot mostra o painel de nós com EVENTOS selecionados.][sfx-eventstore]

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. EventStore não está ativado por padrão e deve ser ativado no modelo de gestor de recursos

>[!NOTE]
>A partir da versão 6.4 do Tecido de Serviço. as APIs da EventStore só estão disponíveis para clusters Windows em execução apenas no Azure. Estamos a trabalhar na deposição desta funcionalidade ao Linux, bem como aos nossos clusters autónomos.

## <a name="image-store-viewer"></a>Visualizador da loja de imagens
O visualizador da loja de imagens é uma funcionalidade oferecida se utilizar a Native Image Store que permite visualizar o conteúdo atual da loja de Imagens e obter informações de ficheiros e pastas, juntamente com a remoção de ficheiros/pastas.

![Screenshot que mostra o Visualizador da Loja de Imagens.][sfx-imagestore]

## <a name="backup-and-restore"></a>Cópia de Segurança e Restauro
O Service Fabric Explorer oferece a capacidade de interagir com [a Cópia de Segurança e Restauro.](./service-fabric-reliable-services-backup-restore.md) Para ver as funcionalidades de Backup e Restauro no SFX, o modo avançado deve ser ativado.

![Ativar o modo avançado][0]
 
São possíveis as seguintes operações:

* Criar, editar e eliminar uma Política de Backup.
* Ative e desative a cópia de segurança para uma aplicação, serviço ou partição.
* Suspenda e retome a cópia de segurança para uma aplicação, serviço ou divisão.
* Detona e rastreie a cópia de segurança de uma partição.
* Gatilho e pista Restaurar para uma divisória.

Para obter mais informações sobre o serviço de backup e restauro, consulte a [referência REST API](/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Passos seguintes
* [Gerir as suas aplicações de Tecido de Serviço em Estúdio Visual](service-fabric-manage-application-in-visual-studio.md)
* [Implementação de aplicação de tecido de serviço usando PowerShell](service-fabric-deploy-remove-applications.md)

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
