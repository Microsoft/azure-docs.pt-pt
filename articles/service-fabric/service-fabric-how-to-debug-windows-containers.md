---
title: Depurar contentores do Windows com o Service Fabric e VS
description: Saiba como depurar os recipientes Windows em Tecido de Serviço Azure utilizando o Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127633"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Como: Debug Windows contentores em Tecido de Serviço Azure usando O Estúdio Visual 2019

Com o Visual Studio 2019, pode depurar as aplicações .NET em contentores como serviços de Fabricação de Serviço. Este artigo mostra-lhe como configurar o seu ambiente e, em seguida, depurar uma aplicação .NET num contentor que funciona num cluster de Tecido de Serviço local.

## <a name="prerequisites"></a>Pré-requisitos

* No Windows 10, siga este quickstart para [configurar o Windows 10 para executar recipientes Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* No Windows Server 2016, siga este quickstart para [configurar o Windows 2016 para executar os recipientes windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Instale o seu ambiente de Tecido de Serviço local seguindo [prepare o seu ambiente](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) de desenvolvimento no Windows

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configure o seu ambiente de desenvolvimento para depurar recipientes

1. Certifique-se de que o serviço Docker for Window está em execução antes de prosseguir com o próximo passo.

1. Para apoiar a resolução de DNS entre contentores, terá de configurar o seu cluster de desenvolvimento local, utilizando o nome da máquina. Estes passos também são necessários se quiser endereçar serviços através do proxy inverso.
   1. Open PowerShell como administrador
   2. Navegue para a pasta de configuração do Cluster SDK, tipicamente `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Executar o guião `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Pode usar o `-CreateOneNodeCluster` para configurar um aglomerado de um nó. O padrão criará um aglomerado local de cinco nós.
      >

      Para saber mais sobre o Serviço DNS em Tecido de Serviço, consulte o [Serviço DNS em Tecido de Serviço Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Para saber mais sobre a utilização do proxy inverso do Tecido de Serviço a partir de serviços que estão a funcionar num contentor, consulte o [manuseamento especial de procuração reversa para serviços em funcionamento em contentores](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitações conhecidas ao depurar contentores em Tecido de Serviço

Abaixo está uma lista de limitações conhecidas com contentores de depuração em Tecido de Serviço e possíveis resoluções:

* A utilização de hospedeiro local para clusterFQDNorIP não suporta a resolução de DNS em contentores.
    * Resolução: Configurar o cluster local utilizando o nome da máquina (ver acima)
* Executar o Windows10 numa Máquina Virtual não vai fazer com que o DNS responda de volta ao recipiente.
    * Resolução: Desativar a descarga de verificação uDP para IPv4 no NIC das Máquinas Virtuais
    * Executar o Windows10 irá degradar o desempenho da rede na máquina.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* A resolução de serviços na mesma aplicação utilizando o nome de serviço DNS não funciona no Windows10, caso a aplicação tenha sido implementada utilizando o Docker Compose
    * Resolução: Use servicename.applicationname para resolver pontos finais do serviço
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Se utilizar o endereço IP para clusterFQDNorIP, alterar o IP primário no hospedeiro quebrará a funcionalidade DNS.
    * Resolução: Recrie o cluster utilizando o novo IP primário no hospedeiro ou utilize o nome da máquina. Esta rutura é por desígnio.
* Se o FQDN com o qual o cluster foi criado não for resolúvel na rede, o DNS falhará.
    * Resolução: Recriar o cluster local utilizando o IP primário do hospedeiro. Esta falha é por desígnio.
* Ao depurar um recipiente, os registos de estivadores só estarão disponíveis na janela de saída do Estúdio Visual, não através de APIs de Tecido de Serviço, incluindo o Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Depurar uma aplicação .NET em contentores de estivadores no Tecido de Serviço

1. Executar o Estúdio Visual como administrador.

1. Abra uma aplicação .NET existente ou crie uma nova.

1. Clique no projeto e selecione **Add -> Container Orchestrator Support -> Service Fabric**

1. Pressione **f5** para começar a depurar a aplicação.

    O Visual Studio suporta os tipos de projetos de consola e ASP.NET para .NET e .NET Core.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as capacidades do Tecido de Serviço e dos recipientes, consulte a visão geral dos [recipientes de tecido de serviço](service-fabric-containers-overview.md).
