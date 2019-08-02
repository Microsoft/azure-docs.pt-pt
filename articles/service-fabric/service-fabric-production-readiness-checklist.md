---
title: Lista de verificação de preparação de produção do Azure Service Fabric | Microsoft Docs
description: Obtenha seu aplicativo Service Fabric e a produção de cluster pronta seguindo as práticas recomendadas.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/05/2019
ms.author: atsenthi
ms.openlocfilehash: 9e86f7306ee70bee2e084b967867e2a9be5b66e1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599352"
---
# <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção

Seu aplicativo e cluster estão prontos para fazer o tráfego de produção? Executar e testar seu aplicativo e seu cluster não significa necessariamente que está pronto para entrar em produção. Mantenha seu aplicativo e o cluster em execução sem problemas passando pela seguinte lista de verificação. É altamente recomendável fazer check-out de todos esses itens. Obviamente, você pode optar por usar soluções alternativas para um item de linha específico (por exemplo, suas próprias estruturas de diagnóstico).


## <a name="prerequisites-for-production"></a>Pré-requisitos para produção
1. Práticas recomendadas do Azure Service Fabric: [Design de aplicativos](./service-fabric-best-practices-applications.md), [segurança](./service-fabric-best-practices-security.md), [rede](./service-fabric-best-practices-networking.md), [planejamento de capacidade e dimensionamento](./service-fabric-best-practices-capacity-scaling.md), [Infraestrutura como código](./service-fabric-best-practices-infrastructure-as-code.md)e [monitoramento e diagnóstico](./service-fabric-best-practices-monitoring.md). 
1. Implemente a configuração de segurança Reliable Actors se estiver usando o modelo de programação atores
1. Para clusters com mais de 20 núcleos ou 10 nós, crie um tipo de nó primário dedicado para serviços do sistema. Adicione [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para reservar o tipo de nó primário para serviços do sistema.
1. Use um SKU D2v2 ou superior para o tipo de nó primário. É recomendável escolher uma SKU com pelo menos 50 GB de capacidade de disco rígido.
1. Os clusters de produção devem ser [seguros](service-fabric-cluster-security.md). Para obter um exemplo de como configurar um cluster seguro, consulte este [modelo de cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Use nomes comuns para certificados e evite usar certificados autoassinados.
1. Adicione [restrições de recursos em contêineres e serviços](service-fabric-resource-governance.md), para que eles não consumam mais de 75% dos recursos de nó. 
1. Entenda e defina o [nível](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)de durabilidade. Um nível de durabilidade prata ou mais alto é recomendado para tipos de nó que executam cargas de trabalho com estado. O tipo de nó primário deve ter um nível de durabilidade definido como prata ou superior.
1. Entenda e escolha o [nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) do tipo de nó. É recomendável uma confiabilidade prateada ou mais alta.
1. Carregue e dimensione teste suas cargas de trabalho para identificar [os requisitos de capacidade](service-fabric-cluster-capacity.md) para seu cluster. 
1. Seus serviços e aplicativos são monitorados e os logs de aplicativo estão sendo gerados e armazenados, com alertas. Por exemplo, consulte [adicionar registro em log ao aplicativo Service Fabric](service-fabric-how-to-diagnostics-log.md) e [monitorar contêineres com Azure monitor logs](service-fabric-diagnostics-oms-containers.md).
1. O cluster é monitorado com alertas (por exemplo, com [logs de Azure monitor](service-fabric-diagnostics-event-analysis-oms.md)). 
1. A infraestrutura subjacente do conjunto de dimensionamento de máquinas virtuais é monitorada com alertas (por exemplo, com [logs de Azure monitor](service-fabric-diagnostics-oms-agent.md).
1. O cluster tem sempre [certificados primários e secundários](service-fabric-cluster-security-update-certs-azure.md) (portanto, você não fica bloqueado).
1. Mantenha clusters separados para desenvolvimento, preparo e produção. 
1. [Atualizações de aplicativos](service-fabric-application-upgrade.md) e [atualizações de cluster](service-fabric-tutorial-upgrade-cluster.md) são testadas primeiro em clusters de desenvolvimento e de preparo. 
1. Desative as atualizações automáticas em clusters de produção e ative-as para clusters de preparo e desenvolvimento (reversão conforme necessário). 
1. Estabeleça um RPO (objetivo de ponto de recuperação) para seu serviço e configure um [processo de recuperação](service-fabric-disaster-recovery.md) de desastres e teste-o.
1. Planeje o [dimensionamento](service-fabric-cluster-scaling.md) manual ou programático do cluster.
1. Planeje a [aplicação](service-fabric-patch-orchestration-application.md) de patches em seus nós de cluster. 
1. Estabeleça um pipeline de CI/CD para que suas alterações mais recentes sejam continuamente testadas. Por exemplo, usando o [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Teste seu desenvolvimento & clusters de preparo sob carga com o [serviço de análise de falha](service-fabric-testability-overview.md) e induz o [caos](service-fabric-controlled-chaos.md)controlado. 
1. Planeje o [dimensionamento](service-fabric-concepts-scalability.md) de seus aplicativos. 


Se você estiver usando o modelo de programação Service Fabric Reliable Services ou Reliable Actors, os itens a seguir precisarão ser desmarcados:
1. Atualizar aplicativos durante o desenvolvimento local para verificar se o código do serviço está respeitando o token de cancelamento no `RunAsync` método e fechando ouvintes de comunicação personalizados.
1. Evite [armadilhas comuns](service-fabric-work-with-reliable-collections.md) ao usar coleções confiáveis.
1. Monitore os contadores de desempenho de memória .NET CLR ao executar testes de carga e verificar se há altas tarifas de coleta de lixo ou crescimento de heap de fuga.
1. Mantenha o backup offline de [Reliable Services e Reliable Actors](service-fabric-reliable-services-backup-restore.md) e teste o processo de restauração.
1. A contagem de instâncias da máquina virtual NodeType primária deve ser ideal para o nível mínimo da camada de confiabilidade dos clusters; as condições quando apropriadas para exceder o mínimo de camada incluem: temporariamente ao dimensionar verticalmente sua SKU do conjunto de dimensionamento de máquinas virtuais de NodeType primário.

## <a name="optional-best-practices"></a>Práticas recomendadas opcionais

Embora as listas acima sejam pré-requisitos para entrar em produção, os itens a seguir também devem ser considerados:
1. Conecte-se ao [modelo de integridade Service Fabric](service-fabric-health-introduction.md) para estender a avaliação de integridade interna e os relatórios.
1. Implante um Watchdog personalizado que esteja monitorando seu aplicativo e relate a [carga](service-fabric-cluster-resource-manager-metrics.md) para o [balanceamento de recursos](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Passos Seguintes
* [Implantar um Service Fabric cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implantar um Cluster Service Fabric Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
