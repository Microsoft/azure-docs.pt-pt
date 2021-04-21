---
title: Introdução
description: Aprenda as funcionalidades e benefícios da Azure VMware Solution para implementar e gerir cargas de trabalho baseadas em VMware em Azure. A Azure VMware Solution SLA garante que as ferramentas de gestão de VMware Azure (vCenter Server e NSX Manager) estarão disponíveis pelo menos 99,9% das vezes.
ms.topic: overview
ms.date: 04/20/2021
ms.openlocfilehash: 9471aa2e427d28e0b8211dc8b25b2e61a4bfa8c4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752647"
---
# <a name="what-is-azure-vmware-solution"></a>O que é o Azure VMware Solution?

A Azure VMware Solution fornece-lhe nuvens privadas que contêm clusters vSphere, construídos a partir de infraestruturas Azure de metal nu dedicada. A implantação inicial mínima é de três anfitriões, mas hospedeiros adicionais podem ser adicionados um de cada vez, até um máximo de 16 anfitriões por cluster.  Todas as nuvens privadas a provisionadas têm vCenter Server, vSAN, vSphere e NSX-T. Pode migrar cargas de trabalho dos seus ambientes no local, implementar novas máquinas virtuais (VMs) e consumir serviços Azure a partir das suas nuvens privadas.  As ferramentas de gestão de Azure VMware (vCenter Server e NSX Manager) estarão disponíveis pelo menos 99,9% das vezes. Para mais informações, consulte [Azure VMware Solution SLA](https://aka.ms/avs/sla).

A Azure VMware Solution é uma solução validada pela VMware com validação e teste em curso de melhorias e upgrades. A Microsoft gere e mantém a infraestrutura e software privados em nuvem. Permite-lhe focar-se no desenvolvimento e execução de cargas de trabalho nas suas nuvens privadas. 

O diagrama mostra a adjacência entre nuvens privadas e VNets em Azure, serviços Azure e ambientes no local. O acesso à rede de nuvens privadas a serviços Azure ou VNets proporciona integração orientada pelo SLA dos pontos finais do serviço Azure. O ExpressRoute Global Reach liga o seu ambiente no local à sua nuvem privada Azure VMware Solution. 

![Imagem de Azure VMware Solution adjacência de nuvem privada para Azure e no local](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

Azure VMware Solution nuvens privadas e clusters são construídos a partir de um hospedeiro de infraestrutura Azure de metal nu e hiper-convergente. Os anfitriões topo de gama têm 576 GB de RAM e processadores dual Intel 18, 2.3-GHz. Os anfitriões he têm dois grupos de discos vSAN com 15,36 TB (SSD) de nível de capacidade vSAN bruto e um nível de cache vSAN de 3.2 TB (NVMe).

Novas nuvens privadas são implantadas através do portal Azure ou Azure CLI.

## <a name="networking"></a>Rede

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Para mais informações, consulte [conceitos de Networking.](concepts-networking.md)

## <a name="access-and-security"></a>Acesso e segurança

As nuvens privadas Azure VMware Solution utilizam o controlo de acesso baseado em funções vSphere para uma maior segurança. Pode integrar as capacidades vSphere SSO LDAP com o Azure Ative Directory. Para mais informações, consulte os [conceitos de Acesso e Identidade.](concepts-identity.md)  

vSAN encriptação de dados em repouso, por padrão, está ativada e é usada para fornecer segurança vSAN datastore. Para mais informações, consulte [os conceitos de Armazenamento.](concepts-storage.md)

## <a name="host-and-software-lifecycle-maintenance"></a>Manutenção do ciclo de vida do hospedeiro e do software

As atualizações regulares do software Azure VMware Solution e software VMware garantem que os mais recentes conjuntos de segurança, estabilidade e funcionalidades estão a ser executadas nas suas nuvens privadas. Para mais informações, consulte [atualizações e atualizações privadas na nuvem.](concepts-upgrades.md)

## <a name="monitoring-your-private-cloud"></a>Monitorização da sua nuvem privada

Assim que a Solução VMware Azure é implantada na sua subscrição, [os registos do Azure Monitor](../azure-monitor/overview.md) são gerados automaticamente. 

Na sua nuvem privada, pode:
- Colete registos em cada um dos seus VMs.
- [Descarregue e instale o agente MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options) em Linux e Windows VMs.
- Ativar a extensão de [diagnóstico do Azure](../azure-monitor/agents/diagnostics-extension-overview.md).
- [Criar e executar novas consultas.](../azure-monitor/logs/data-platform-logs.md#log-queries)
- Faça as mesmas perguntas que normalmente corre nos seus VMs.

Os padrões de monitorização dentro da Solução VMware Azure são semelhantes aos VMs Azure dentro da plataforma IaaS. Para obter mais informações e como fazer, consulte [monitorar VMs Azure com Azure Monitor](../azure-monitor/vm/monitor-vm-azure.md).

## <a name="customer-communication"></a>Comunicação ao cliente
[!INCLUDE [customer-communications](includes/customer-communications.md)]

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender os principais [conceitos de nuvem privada e cluster.](concepts-private-clouds-clusters.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md


