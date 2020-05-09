---
title: Atualizar um cluster autónomo de Tecido de Serviço Azure
description: Aprenda sobre a atualização da versão ou configuração de um cluster autónomo do Tecido de Serviço Azure.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: ac3e80aae440c6709057e4211f7a4f6a1beea058
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790648"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Atualizar e atualizar um cluster autónomo de Tecido de Serviço

Para qualquer sistema moderno, conceber para a graduação é fundamental para alcançar o sucesso a longo prazo do seu produto. Um cluster autónomo azure Service Fabric é um recurso que possui. Este artigo descreve o que pode ser atualizado ou atualizado.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de tecido que corre no seu cluster
Certifique-se de que o seu cluster executa sempre uma [versão de Tecido de Serviço suportado](service-fabric-versions.md). Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias a contar da data do anúncio. Novos lançamentos são anunciados [no blog da equipa de Service Fabric.](https://blogs.msdn.microsoft.com/azureservicefabric/) O novo lançamento está disponível para escolher nessa altura.

Pode configurar o seu cluster para receber atualizações automáticas de tecido saem à medida que são lançadas pela Microsoft ou pode selecionar manualmente uma versão de tecido suportado em que pretende que o seu cluster esteja ligado. Para mais informações, leia [Atualizar a versão Service Fabric que corre no seu cluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Personalizar as definições de configuração

Muitas configurações de [configuração diferentes](service-fabric-cluster-manifest.md) podem ser definidas no ficheiro *ClusterConfig.json,* como o nível de fiabilidade das propriedades do cluster e do nó.  Para saber mais, leia [Atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).  Muitas outras configurações, mais avançadas, também podem ser personalizadas.  Para mais informações, leia [as definições](service-fabric-cluster-fabric-settings.md)de tecido de cluster de tecido de serviço .

## <a name="define-node-properties"></a>Definir propriedades do nó
Por vezes, é possível garantir que determinadas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs, enquanto outros não podem. Para cada um dos tipos de nós em um cluster, você pode adicionar propriedades personalizadas do nó aos nós de cluster. Os constrangimentos de colocação são as declarações anexas a serviços individuais que selecionam para uma ou mais propriedades do nó. Os constrangimentos de colocação definem onde os serviços devem ser executados.

Para mais detalhes sobre a utilização de restrições de colocação, propriedades do nó e como defini-las, leia as propriedades do nó e os constrangimentos de [colocação.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)
 

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade
Para cada um dos tipos de nós, pode adicionar métricas de capacidade personalizadas que pretende utilizar nas suas aplicações para reportar carga. Para obter mais detalhes sobre a utilização de métricas de capacidade para reportar a carga, consulte os documentos do Gestor de Recursos de Cluster de Cluster de Tecidos de Serviço sobre [a descrição](service-fabric-cluster-resource-manager-cluster-description.md) do seu cluster e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Remendar o SO nos nós do cluster
A aplicação de orquestração de patch (POA) é uma aplicação service Fabric que automatiza o sistema operativo patching em um cluster de Tecido de Serviço sem tempo de inatividade. A Aplicação de [Orquestração de Patch para Windows](service-fabric-patch-orchestration-application.md) pode ser implementada no seu cluster para instalar patches de forma orquestrada, mantendo os serviços sempre disponíveis. 


## <a name="next-steps"></a>Passos seguintes
* Aprenda a personalizar algumas das [configurações](service-fabric-cluster-fabric-settings.md) de tecido de tecido de serviço
* Aprenda a [escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md)
* Conheça as atualizações de [aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
