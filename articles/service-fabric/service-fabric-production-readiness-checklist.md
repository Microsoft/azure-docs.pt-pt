---
title: Lista de verificação de prontidão de produção de tecido de serviço Azure
description: Prepare a sua aplicação Service Fabric e a produção de cluster seguindo as melhores práticas.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75376805"
---
# <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção

A sua aplicação e cluster estão prontos para tomar tráfego de produção? Correr e testar a sua aplicação e o seu cluster não significa necessariamente que esteja pronto para entrar em produção. Mantenha a sua aplicação e cluster funcionando sem problemas, passando pela seguinte lista de verificação. Recomendamos vivamente que todos estes itens sejam verificados. Obviamente, pode optar por utilizar soluções alternativas para um determinado item de linha (por exemplo, os seus próprios quadros de diagnóstico).


## <a name="prerequisites-for-production"></a>Pré-requisitos para a produção
1. Boas práticas do Tecido de Serviço Azure: [Design de Aplicações,](./service-fabric-best-practices-applications.md) [Segurança,](./service-fabric-best-practices-security.md) [Networking,](./service-fabric-best-practices-networking.md) [Planeamento de Capacidadee escala,](./service-fabric-best-practices-capacity-scaling.md) [Infraestrutura como Código](./service-fabric-best-practices-infrastructure-as-code.md)e [Monitorização e Diagnósticos.](./service-fabric-best-practices-monitoring.md) 
1. Implementar a configuração de segurança de Atores Fiáveis se utilizar o modelo de programação dos Atores
1. Para clusters com mais de 20 núcleos ou 10 nós, crie um nó primário dedicado para serviços de sistema. Adicione restrições de [colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para reservar o tipo principal de nó para os serviços do sistema.
1. Utilize um SKU D2v2 ou superior para o tipo de nó primário. Recomenda-se escolher um SKU com capacidade de disco rígido de pelo menos 50 GB.
1. Os aglomerados de produção devem ser [seguros.](service-fabric-cluster-security.md) Para um exemplo de configuração de um cluster seguro, consulte este modelo de [cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Utilize nomes comuns para certificados e evite usar certs auto-assinados.
1. Adicione restrições de [recursos em contentores e serviços,](service-fabric-resource-governance.md)para que não consumam mais de 75% dos recursos do nó. 
1. Compreender e definir o [nível de durabilidade.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) O nível de durabilidade prateado ou superior é recomendado para tipos de nó que executam cargas de trabalho estatais. O tipo de nó primário deve ter um nível de durabilidade definido para Prata ou superior.
1. Compreenda e escolha o nível de [fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) do tipo de nó. Recomenda-se a fiabilidade prateada ou superior.
1. A carga e a escala testam as suas cargas de trabalho para identificar [os requisitos](service-fabric-cluster-capacity.md) de capacidade para o seu cluster. 
1. Os seus serviços e aplicações são monitorizados e os registos de aplicações estão a ser gerados e armazenados, com alerta. Por exemplo, consulte [Adicionar sessão de registo à sua aplicação de tecido de serviço](service-fabric-how-to-diagnostics-log.md) e monitorizar [recipientes com registos do Monitor Azure](service-fabric-diagnostics-oms-containers.md).
1. O cluster é monitorizado com alerta (por exemplo, com [registos do Monitor Azure).](service-fabric-diagnostics-event-analysis-oms.md) 
1. A infraestrutura de conjunto de máquinas virtuais subjacente é monitorizada com alerta (por exemplo, com [registos do Monitor Azure](service-fabric-diagnostics-oms-agent.md).
1. O cluster tem sempre [certificados primários e secundários](service-fabric-cluster-security-update-certs-azure.md) (para que não fique trancado).
1. Manter aglomerados separados para desenvolvimento, encenação e produção. 
1. [As atualizações de aplicações](service-fabric-application-upgrade.md) e [as atualizações](service-fabric-tutorial-upgrade-cluster.md) de clusters são testadas em clusters de desenvolvimento e encenação primeiro. 
1. Desligue as atualizações automáticas nos clusters de produção e ligue-as para o desenvolvimento e encenação de clusters (reversão conforme necessário). 
1. Estabeleça um Objetivo de Ponto de Recuperação (RPO) para o seu serviço e estabeleça um processo de recuperação de [desastres](service-fabric-disaster-recovery.md) e teste-o.
1. [Planeie escalonar](service-fabric-cluster-scaling.md) o seu cluster manualmente ou programáticamente.
1. [Planeie remendar](service-fabric-patch-orchestration-application.md) os seus nós de cluster. 
1. Estabeleça um gasoduto CI/CD de modo a que as suas últimas alterações estejam a ser continuamente testadas. Por exemplo, usando [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Teste o seu desenvolvimento & a encenar clusters sob carga com o Serviço de Análise de [Falhas](service-fabric-testability-overview.md) e induzir [o caos](service-fabric-controlled-chaos.md)controlado. 
1. [Planeie escalonar](service-fabric-concepts-scalability.md) as suas candidaturas. 


Se estiver a utilizar o modelo de programação Service Fabric Reliable Services ou Reliable Actors, os seguintes itens devem ser verificados:
1. Atualize as aplicações durante o desenvolvimento local para verificar se `RunAsync` o seu código de serviço está honrando o símbolo de cancelamento no método e fechando os ouvintes de comunicação personalizados.
1. Evite [armadilhas comuns](service-fabric-work-with-reliable-collections.md) ao utilizar Coleções Fiáveis.
1. Monitorize os contadores de desempenho da memória CLR .NET ao executar testes de carga e verifique se há altas taxas de Recolha de Lixo ou crescimento de pilha em fuga.
1. Mantenha a cópia de segurança offline de [Serviços Fiáveis e Atores Fiáveis](service-fabric-reliable-services-backup-restore.md) e teste o processo de restauração.
1. A contagem de instâncias de máquina virtual do nó primário deve ser idealmente igual ao mínimo para o seu nível de Fiabilidade dos Clusters; as condições, quando apropriado para exceder o nível mínimo, inclui: temporariamente quando escalaverticalmente o seu conjunto de escala virtual SKU do nó primário.

## <a name="optional-best-practices"></a>Boas práticas opcionais

Embora as listas acima referidas sejam pré-requisitos para entrar em produção, devem também ser considerados os seguintes itens:
1. Ligue-se ao modelo de [saúde do Tecido de Serviço](service-fabric-health-introduction.md) para alargar a avaliação e relatório saúde incorporados.
1. Implemente um cão de guarda personalizado que esteja a monitorizar a sua aplicação e reporte [carga](service-fabric-cluster-resource-manager-metrics.md) para [o equilíbrio de recursos](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Passos seguintes
* [Implementar um cluster de janelas de tecido de serviço](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implementar um cluster de Linux de Tecido de Serviço](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
