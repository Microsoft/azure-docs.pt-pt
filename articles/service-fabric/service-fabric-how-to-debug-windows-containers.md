---
title: Depurar contentores do Windows com o Service Fabric e VS
description: Saiba como depurar contêineres do Windows no Azure Service Fabric usando o Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464564"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Como: Depurar contêineres do Windows no Azure Service Fabric usando o Visual Studio 2019

Com o Visual Studio 2019, você pode depurar aplicativos .NET em contêineres como Service Fabric Services. Este artigo mostra como configurar seu ambiente e depurar um aplicativo .NET em um contêiner em execução em um cluster de Service Fabric local.

## <a name="prerequisites"></a>Pré-requisitos

* No Windows 10, siga este guia de início rápido para [Configurar o Windows 10 para executar contêineres do Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* No Windows Server 2016, siga este guia de início rápido para [Configurar o windows 2016 para executar contêineres do Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurar seu ambiente de Service Fabric local seguindo [preparar seu ambiente de desenvolvimento no Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configurar seu ambiente de desenvolvedor para depurar contêineres

1. Verifique se o Docker para o serviço Window está em execução antes de prosseguir com a próxima etapa.

1. Para dar suporte à resolução de DNS entre contêineres, você precisará configurar seu cluster de desenvolvimento local usando o nome do computador. Essas etapas também serão necessárias se você quiser endereçar serviços por meio do proxy reverso.
   1. Abrir o PowerShell como administrador
   2. Navegue até a pasta de instalação do cluster do SDK, normalmente `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Execute o script `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Você pode usar o `-CreateOneNodeCluster` para configurar um cluster de um nó. O padrão criará um cluster local de cinco nós.
      >

      Para saber mais sobre o serviço DNS no Service Fabric, consulte [serviço DNS no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Para saber mais sobre como usar Service Fabric proxy reverso de serviços em execução em um contêiner, consulte [tratamento especial de proxy reverso para serviços em execução em contêineres](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitações conhecidas ao depurar contêineres no Service Fabric

Abaixo está uma lista de limitações conhecidas com contêineres de depuração em Service Fabric e possíveis resoluções:

* Usar localhost para ClusterFQDNorIP não dará suporte à resolução de DNS em contêineres.
    * Resolução: Configure o cluster local usando o nome do computador (veja acima)
* Executar o Windows10 em uma máquina virtual não fará a resposta DNS de volta para o contêiner.
    * Resolução: desabilitar o descarregamento de soma de verificação UDP para IPv4 na NIC de máquinas virtuais
    * A execução de Windows10 diminuirá o desempenho da rede no computador.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Resolver serviços no mesmo aplicativo usando o nome do serviço DNS não funciona em Windows10, se o aplicativo tiver sido implantado usando Docker Compose
    * Resolução: Use ServiceName. ApplicationName para resolver pontos de extremidade de serviço
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Se você estiver usando o endereço IP para ClusterFQDNorIP, a alteração do IP primário no host interromperá a funcionalidade do DNS.
    * Resolução: recrie o cluster usando o novo IP primário no host ou use o nome do computador. Essa quebra é por design.
* Se o FQDN no qual o cluster foi criado não puder ser resolvido na rede, o DNS falhará.
    * Resolução: recrie o cluster local usando o IP primário do host. Essa falha é por design.
* Ao depurar um contêiner, os logs do Docker só estarão disponíveis na janela de saída do Visual Studio, não por meio de APIs de Service Fabric, incluindo Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Depurar um aplicativo .NET em execução em contêineres do Docker no Service Fabric

1. Execute o Visual Studio como administrador.

1. Abra um aplicativo .NET existente ou crie um novo.

1. Clique com o botão direito do mouse no projeto e selecione **adicionar > contêiner de suporte do Orchestrator-> Service Fabric**

1. Pressione **F5** para iniciar a depuração do aplicativo.

    O Visual Studio dá suporte a tipos de projeto de console e ASP.NET para .NET e .NET Core.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os recursos de Service Fabric e contêineres, consulte [visão geral de contêineres de Service Fabric](service-fabric-containers-overview.md).
