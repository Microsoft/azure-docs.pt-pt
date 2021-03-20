---
title: Depurar contentores do Windows com o Service Fabric e VS
description: Saiba como depurar recipientes Windows em Azure Service Fabric utilizando o Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 3e6e7785278b182cebb21115a70f35ade52303c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247256"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Como: Debug Recipientes Windows em Tecido de Serviço Azure usando Visual Studio 2019

Com o Visual Studio 2019, pode depurar aplicações .NET em contentores como serviços de Service Fabric. Este artigo mostra-lhe como configurar o seu ambiente e, em seguida, depurar uma aplicação .NET num recipiente que funciona num cluster de Tecido de Serviço local.

## <a name="prerequisites"></a>Pré-requisitos

* No Windows 10, siga este quickstart para [configurar o Windows 10 para executar recipientes Windows](/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* No Windows Server 2016, siga este quickstart para [configurar o Windows 2016 para executar contentores Windows](/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurar o seu ambiente de Tecido de Serviço local, seguindo [o seu ambiente de desenvolvimento no Windows](./service-fabric-get-started.md)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configure o seu ambiente de desenvolvimento para depurar contentores

1. Certifique-se de que o serviço de janelas do Docker está a funcionar antes de prosseguir com o próximo passo.

1. Para apoiar a resolução de DNS entre contentores, terá de configurar o seu cluster de desenvolvimento local, utilizando o nome da máquina. Estes passos também são necessários se pretender dirigir-se aos serviços através do representante inverso.
   1. Open PowerShell como administrador
   2. Navegue para a pasta de configuração do Cluster SDK, normalmente `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup` .
   3. Executar o script `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Pode utilizar `-CreateOneNodeCluster` o para configurar um agrupamento de um nó. O padrão criará um aglomerado local de cinco nós.
      >

      Para saber mais sobre o Serviço DNS em Tecido de Serviço, consulte o [Serviço DNS em Azure Service Fabric.](./service-fabric-dnsservice.md) Para saber mais sobre a utilização de proxy reverso do Service Fabric a partir de serviços em execução num contentor, consulte [o tratamento especial de procuração inversa para serviços em funcionamento em contentores.](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitações conhecidas ao depurar contentores em tecido de serviço

Abaixo está uma lista de limitações conhecidas com depurações de contentores em Tecido de Serviço e possíveis resoluções:

* A utilização de locais para clusterFQDNorIP não suporta a resolução de DNS em contentores.
    * Resolução: Configurar o cluster local utilizando o nome da máquina (ver acima)
* Executar o Windows10 numa Máquina Virtual não receberá resposta de DNS de volta ao recipiente.
    * Resolução: Desativar o descarregamento de checkum da UDP para o IPv4 no NIC das Máquinas Virtuais
    * O funcionamento do Windows10 irá degradar o desempenho da rede na máquina.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* A resolução de serviços na mesma aplicação utilizando o nome do serviço DNS não funciona no Windows10, se a aplicação foi implementada usando o Docker Compose
    * Resolução: Use nome de serviço.nome de aplicação para resolver pontos finais de serviço
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Se utilizar o endereço IP para ClusterFQDNorIP, alterar o IP primário no hospedeiro quebrará a funcionalidade DNS.
    * Resolução: Recriar o cluster utilizando o novo IP primário no anfitrião ou utilizar o nome da máquina. Esta rutura é por desígnio.
* Se o FQDN com o qual o cluster foi criado não for resolúvel na rede, o DNS falhará.
    * Resolução: Recriar o cluster local utilizando o IP primário do hospedeiro. Esta falha é por desígnio.
* Ao depurar um contentor, os registos de estivadores só estarão disponíveis na janela de saída do Estúdio Visual, não através de APIs de Tecido de Serviço, incluindo o Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Depurar uma aplicação .NET em execução em contentores de estivadores em Tecido de Serviço

1. Executar o Estúdio Visual como administrador.

1. Abra uma aplicação .NET existente ou crie uma nova.

1. Clique com o botão direito no projeto e **selecione Add -> Container Orchestrator Support -> Service Fabric**

1. Prima **F5** para começar a depurar a aplicação.

    O Visual Studio suporta os tipos de consolas e ASP.NET de projetos para .NET e .NET Core.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as capacidades do Tecido de Serviço e dos recipientes, consulte a [visão geral dos recipientes de Tecido de Serviço.](service-fabric-containers-overview.md)
