---
title: Atualizar um cluster autônomo do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre como atualizar a versão ou a configuração de um cluster autônomo do Azure Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: bf99d5d59354745508d8ca88abfc4b42fe608025
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599798"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Atualizando e atualizando um Cluster Service Fabric autônomo

Para qualquer sistema moderno, a criação de uma grande possibilidade é a chave para atingir o sucesso de longo prazo de seu produto. Um cluster autônomo do Azure Service Fabric é um recurso que você possui. Este artigo descreve o que pode ser atualizado ou atualizado.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão da malha que é executada no cluster
Certifique-se de que o cluster sempre execute uma [versão de Service Fabric com suporte](service-fabric-versions.md). Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir da data do anúncio. Novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para escolha nesse ponto.

Você pode definir seu cluster para receber atualizações automáticas de malha à medida que elas são lançadas pela Microsoft ou você pode selecionar manualmente uma versão de malha com suporte na qual você deseja que o cluster esteja. Para obter mais informações, leia [atualizar a versão de Service Fabric que é executada no cluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Personalizar definições de configuração

Muitas [definições de configuração](service-fabric-cluster-manifest.md) diferentes podem ser definidas no arquivo *ClusterConfig. JSON* , como o nível de confiabilidade das propriedades do cluster e do nó.  Para saber mais, leia [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md).  Muitas outras configurações, mais avançadas, também podem ser personalizadas.  Para obter mais informações, leia [Service Fabric configurações de malha de cluster](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definir propriedades de nó
Às vezes, talvez você queira garantir que determinadas cargas de trabalho sejam executadas apenas em determinados tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs, enquanto outras não podem. Para cada um dos tipos de nó em um cluster, você pode adicionar propriedades de nó personalizadas a nós de cluster. As restrições de posicionamento são as instruções anexadas a serviços individuais que selecionam uma ou mais propriedades de nó. As restrições de posicionamento definem onde os serviços devem ser executados.

Para obter detalhes sobre o uso de restrições de posicionamento, propriedades de nó e como defini-las, leia [as propriedades do nó e as restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade
Para cada um dos tipos de nó, você pode adicionar métricas de capacidade personalizadas que deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar carga, consulte os documentos Service Fabric cluster Resource Manager sobre como [descrever o cluster e as](service-fabric-cluster-resource-manager-cluster-description.md) [métricas e a carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Corrigir o sistema operacional nos nós do cluster
O POA (aplicativo de orquestração de patch) é um aplicativo Service Fabric que automatiza a aplicação de patch do sistema operacional em um Cluster Service Fabric sem tempo de inatividade. O [aplicativo de orquestração de patch para Windows](service-fabric-patch-orchestration-application.md) pode ser implantado em seu cluster para instalar patches de maneira orquestrada enquanto mantém os serviços disponíveis o tempo todo. 


## <a name="next-steps"></a>Passos Seguintes
* Saiba como personalizar algumas das configurações de [malha de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar o cluster para dentro e para fora](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [atualizações de aplicativos](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
