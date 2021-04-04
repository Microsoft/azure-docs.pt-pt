---
title: Lista de verificação de prontidão de produção de tecido de serviço Azure
description: Prepare a sua aplicação de Tecido de Serviço e produção de clusters seguindo as melhores práticas.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: a03df40a8ce213c5de9ed7017d47713c4de3449d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87835872"
---
# <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção

A sua aplicação e cluster estão prontos para levar o tráfego de produção? Correr e testar a sua aplicação e o seu cluster não significa necessariamente que está pronto para entrar em produção. Mantenha a sua aplicação e cluster a funcionar sem problemas através da seguinte lista de verificação. Recomendamos vivamente que todos estes itens sejam verificados. Obviamente, pode optar por utilizar soluções alternativas para um determinado item de linha (por exemplo, os seus próprios quadros de diagnóstico).


## <a name="prerequisites-for-production"></a>Pré-requisitos para a produção
1. Boas práticas do Azure Service Fabric: [Design de Aplicações,](./service-fabric-best-practices-applications.md) [Segurança,](./service-fabric-best-practices-security.md) [Networking,](./service-fabric-best-practices-networking.md) [Planeamento e Dimensionamento de Capacidades,](./service-fabric-best-practices-capacity-scaling.md) [Infraestruturas como Código,](./service-fabric-best-practices-infrastructure-as-code.md) [Monitorização e Diagnóstico.](./service-fabric-best-practices-monitoring.md) 
1. [Configure as definições do FabricTransport](./service-fabric-reliable-actors-fabrictransportsettings.md) se estiver a utilizar o modelo de programação dos Reliable Actors e necessitar de uma comunicação inter-serviço segura.
1. Para clusters com mais de 20 núcleos ou 10 nós, crie um tipo de nó primário dedicado para serviços de sistema. Adicione [restrições de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para reservar o tipo de nó primário para serviços de sistema.
1. Utilize um D2v2 ou um SKU superior para o tipo de nó primário. Recomenda-se a escolha de um SKU com uma capacidade de disco rígido de pelo menos 50 GB.
1. Os aglomerados de produção devem ser [seguros.](service-fabric-cluster-security.md) Para um exemplo de configuração de um cluster seguro, consulte este [modelo de cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Use nomes comuns para certificados e evite usar certificados auto-assinados.
1. Adicione [restrições de recursos em contentores e serviços,](service-fabric-resource-governance.md)para que não consumam mais de 75% dos recursos de nó. 
1. Compreender e definir o [nível de durabilidade.](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Recomenda-se o nível de durabilidade prateado ou superior para os tipos de nó que executam cargas de trabalho imponentes.
1. Compreenda e escolha o nível de [fiabilidade](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) do tipo nó. Recomenda-se a fiabilidade prateada ou superior.
1. A carga e a escala testam as suas cargas de trabalho para identificar [os requisitos de capacidade](service-fabric-cluster-capacity.md) do seu cluster. 
1. Os seus serviços e aplicações são monitorizados e os registos de aplicações estão a ser gerados e armazenados, com alerta. Por exemplo, consulte [adicionar registos à sua aplicação de Tecido de Serviço](service-fabric-how-to-diagnostics-log.md) e [monitorize os recipientes com registos do Monitor Azure](service-fabric-diagnostics-oms-containers.md).
1. O cluster é monitorizado com alerta (por exemplo, com [registos do Monitor Azure).](service-fabric-diagnostics-event-analysis-oms.md) 
1. A infraestrutura de conjunto de escala de máquina virtual subjacente é monitorizada com alerta (por exemplo, com [registos do Azure Monitor](service-fabric-diagnostics-oms-agent.md).
1. O cluster tem [sempre certificados primários e secundários](service-fabric-cluster-security-update-certs-azure.md) (para que não fique bloqueado).
1. Manter agrupamentos separados para desenvolvimento, encenação e produção. 
1. [As atualizações de aplicações](service-fabric-application-upgrade.md) e [os upgrades de cluster](service-fabric-tutorial-upgrade-cluster.md) são testados em clusters de desenvolvimento e de paragem em primeiro lugar. 
1. Desligue as atualizações automáticas em clusters de produção e ligue-as para o desenvolvimento e a paragem dos clusters (reversão conforme necessário). 
1. Estabeleça um Objetivo de Ponto de Recuperação (RPO) para o seu serviço e crie um [processo de recuperação de desastres](service-fabric-disaster-recovery.md) e teste-o.
1. Planeie [escalar](service-fabric-cluster-scaling.md) o seu cluster manualmente ou programático.
1. Plano para [remendar os](service-fabric-patch-orchestration-application.md) seus nós de cluster. 
1. Estabeleça um pipeline CI/CD para que as suas últimas alterações sejam continuamente testadas. Por exemplo, usando [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) ou [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster)
1. Teste o seu desenvolvimento & os aglomerados de paragem sob carga com o [Serviço de Análise de Falhas](service-fabric-testability-overview.md) e induza o [caos](service-fabric-controlled-chaos.md)controlado . 
1. Plano para [escalonar as](service-fabric-concepts-scalability.md) suas candidaturas. 


Se estiver a utilizar o modelo de programação Service Fabric Reliable Services ou Reliable Actors, os seguintes itens têm de ser verificados:
1. Atualize as aplicações durante o desenvolvimento local para verificar se o seu código de serviço está a honrar o sinal de cancelamento no `RunAsync` método e a fechar os ouvintes de comunicação personalizados.
1. Evite [armadilhas comuns](service-fabric-work-with-reliable-collections.md) quando utilizar Coleções Fiáveis.
1. Monitorize os contadores de desempenho da memória .NET CLR ao executar testes de carga e verifique se há altas taxas de recolha de lixo ou crescimento de pilhas em fuga.
1. Mantenha a cópia de segurança offline dos [Serviços Fiáveis e dos Atores Fiáveis](service-fabric-reliable-services-backup-restore.md) e teste o processo de restauração.
1. A contagem de instâncias da máquina virtual no Tipo Primário deve ser idealmente igual ao mínimo para o seu nível de fiabilidade dos clusters; as condições quando adequadas para exceder o mínimo de nível incluem: temporariamente ao escalar verticalmente o conjunto de escala de máquina virtual de nodeTypes primário SKU.

## <a name="optional-best-practices"></a>Melhores práticas opcionais

Embora as listas acima referidas sejam pré-requisitos para entrar em produção, devem igualmente ser considerados os seguintes elementos:
1. Ligue-se ao modelo de [saúde do Service Fabric](service-fabric-health-introduction.md) para alargar a avaliação e reportagem de saúde incorporadas.
1. Implemente um cão de guarda personalizado que esteja a monitorizar a sua aplicação e reporte [a carga](service-fabric-cluster-resource-manager-metrics.md) para equilibrar [os recursos](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Passos seguintes
* [Implementar um cluster de janelas de tecido de serviço](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implementar um cluster de tecido de serviço Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.