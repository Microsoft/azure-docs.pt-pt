---
title: Atualizar um cluster autónomo de tecido de serviço Azure
description: Saiba como atualizar a versão ou configuração de um cluster autónomo de Tecido de Serviço Azure.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 519a9163d16fda2dd9fcf49cf22fe4ad4a272e09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260994"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Modernização e atualização de um cluster autónomo de Tecido de Serviço

Para qualquer sistema moderno, o design para a atualização é fundamental para alcançar o sucesso a longo prazo do seu produto. Um cluster autónomo de tecido de serviço Azure é um recurso que possui. Este artigo descreve o que pode ser atualizado ou atualizado.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de tecido que funciona no seu cluster
Certifique-se de que o seu cluster executa sempre uma [versão de Tecido de Serviço suportado](service-fabric-versions.md). Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior fica marcada para o fim do suporte após um mínimo de 60 dias a contar da data do anúncio. Novos lançamentos são anunciados [no blog da equipa de Service Fabric.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) O novo lançamento está disponível para escolher nessa altura.

Pode configurar o seu cluster para receber atualizações automáticas de tecidos à medida que forem lançadas pela Microsoft ou pode selecionar manualmente uma versão de tecido suportado que pretende que o seu cluster esteja ligado. Para mais informações, leia [a versão 'Actualizar' o Tecido de Serviço que funciona no seu cluster.](service-fabric-cluster-upgrade-windows-server.md)

## <a name="customize-configuration-settings"></a>Personalizar configurações de configuração

Muitas [configurações diferentes](service-fabric-cluster-manifest.md) podem ser definidas no *ClusterConfig.jsem* arquivo, tais como o nível de fiabilidade das propriedades do cluster e do nó.  Para saber mais, leia [Atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).  Muitas outras configurações, mais avançadas, também podem ser personalizadas.  Para mais informações, leia [as definições de tecido de cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definir propriedades de nó
Por vezes, pode querer garantir que determinadas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs enquanto outros não. Para cada um dos tipos de nó num cluster, pode adicionar propriedades de nó personalizado aos nós de cluster. Constrangimentos de colocação são as declarações anexas a serviços individuais que selecionam para uma ou mais propriedades de nó. Os constrangimentos de colocação definem onde os serviços devem ser executados.

Para mais detalhes sobre a utilização de restrições de colocação, propriedades de nó e como defini-las, leia [propriedades de nó e restrições de colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Adicione métricas de capacidade
Para cada um dos tipos de nó, pode adicionar métricas de capacidade personalizadas que pretende utilizar nas suas aplicações para reportar a carga. Para obter detalhes sobre a utilização de métricas de capacidade para reportar a carga, consulte os documentos do Gestor de Recursos do Cluster de Tecidos de Serviço na [descrição do seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Remendar o SO nos nos nosmes de cluster
A aplicação de orquestração de patch (POA) é uma aplicação de Tecido de Serviço que automatiza o patching do sistema operativo num cluster de Tecido de Serviço sem tempo de inatividade. A [Aplicação de Orquestração de Patch para Windows](service-fabric-patch-orchestration-application.md) pode ser implantada no seu cluster para instalar patches de forma orquestrada, mantendo os serviços disponíveis a toda a hora. 


## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas das [configurações](service-fabric-cluster-fabric-settings.md) do tecido de serviço
* Saiba como [escalar o seu cluster dentro e fora](service-fabric-cluster-scale-in-out.md)
* Saiba mais [sobre atualizações de aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
