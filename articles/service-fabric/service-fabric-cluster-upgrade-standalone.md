---
title: Atualizar um cluster autónomo de Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre como atualizar a versão ou a configuração de um cluster autónomo de Azure Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 1d96a2e81917af5e80bb847ea25610ccb71ad70f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711062"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>A atualizar e a atualizar um cluster autónomo do Service Fabric

Para qualquer sistema moderno, a criação de uma upgradability é a chave para alcançar o sucesso a longo prazo de seu produto. Um cluster autónomo de Azure Service Fabric é um recurso que é proprietário. Este artigo descreve o que pode ser atualizado ou atualizado.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão de recursos de infraestrutura que é executado no seu cluster
Certifique-se de que o cluster é sempre executado um [suportada a versão do Service Fabric](service-fabric-versions.md). Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias a contar da data do anúncio. Novas versões sejam anunciados [no blog da equipe de recursos de infraestrutura do serviço](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para escolher nesse ponto.

Pode definir o seu cluster para receber atualizações de recursos de infraestrutura automático à medida que são lançadas pela Microsoft ou pode selecionar manualmente uma versão de recursos de infraestrutura suportadas que pretende que o cluster na. Para obter mais informações, leia [atualizar a versão de Service Fabric que é executado no seu cluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Personalizar as definições de configuração

Muito diferentes [definições de configuração](service-fabric-cluster-manifest.md) podem ser definidas no *ClusterConfig.json* arquivo, como o nível de fiabilidade das propriedades do cluster e nó.  Para saber mais, leia [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).  Também podem ser personalizadas muitas outras, mais avançados, as definições.  Para obter mais informações, leia [definições de recursos de infraestrutura de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definir propriedades de nó
Por vezes, pode querer garantir que determinadas cargas de trabalho são executados apenas em determinados tipos de nós do cluster. Por exemplo, pode exigir alguma carga de trabalho GPUs ou SSDs, enquanto outros podem não ter. Para cada tipo de nó num cluster, pode adicionar propriedades de nó personalizado para nós de cluster. Restrições de posicionamento são as declarações anexadas aos serviços individuais que selecionar para uma ou mais propriedades de nó. Restrições de posicionamento definem em que os serviços devem ser executados.

Para obter detalhes sobre a utilização de restrições de posicionamento, propriedades de nó e como defini-las, leia [propriedades de nó e restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade
Para cada tipo de nó, pode adicionar métricas de capacidade personalizado que pretende utilizar nas suas aplicações para comunicar carga. Para obter detalhes sobre o uso de métricas de capacidade para comunicar carga, consulte os documentos de Gestor de recursos de Cluster do Service Fabric no [que descreva o seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Patch do SO de nós de cluster
O aplicativo de orquestração do patch (POA) é uma aplicação do Service Fabric que automatiza o sistema operativo, aplicação de patches no cluster do Service Fabric sem tempo de inatividade. O [Patch Orchestration Application para Windows](service-fabric-patch-orchestration-application.md) pode ser implementado no seu cluster para instalar os patches de forma organizada, mantendo os serviços disponíveis, o tempo todo. 


## <a name="next-steps"></a>Passos Seguintes
* Saiba como personalizar algumas do [definições de recursos de infraestrutura de cluster de recursos de infraestrutura do serviço](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar e reduzir o seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [as atualizações de aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
