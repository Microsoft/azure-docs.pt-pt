---
title: Introdução
description: Aprenda as funcionalidades e benefícios da Azure VMware Solution para implementar e gerir cargas de trabalho baseadas em VMware em Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 1875f535a5ca3e346362409c143da82cb8c07a12
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488592"
---
# <a name="what-is-azure-vmware-solution"></a>O que é o Azure VMware Solution?

A Azure VMware Solution fornece-lhe nuvens privadas em Azure. As clouds privadas contêm clusters do vSphere, criadas na infraestrutura do Azure bare-metal dedicada. Pode dimensionar os clusters das clouds privadas de 3 para 16 anfitriões, com capacidade para vários clusters numa única cloud privada. Todas as nuvens privadas são avisadas com vCenter Server, vSAN, vSphere e NSX-T. Pode migrar cargas de trabalho dos ambientes no local, criar ou implementar novas máquinas virtuais e consumir serviços do Azure a partir das clouds privadas.

A Azure VMware Solution é uma solução validada pela VMware com validação e teste em curso de melhorias e upgrades. A infraestrutura e software de nuvem privada são geridos e mantidos pela Microsoft, permitindo-lhe focar-se no desenvolvimento e execução de cargas de trabalho nas suas nuvens privadas.

O diagrama seguinte mostra a adjacência entre nuvens privadas e VNets em Azure, serviços Azure e ambientes no local. O acesso à rede de nuvens privadas a serviços Azure ou VNets proporciona integração orientada pelo SLA dos pontos finais do serviço Azure. O acesso privado à nuvem a partir de ambientes no local utiliza o ExpressRoute Global Reach para uma ligação privada e segura.

![Imagem de Azure VMware Solution adjacência de nuvem privada para Azure e no local](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

Azure VMware Solution nuvens privadas e clusters são construídos a partir de um hospedeiro de infraestrutura Azure de metal nu e hiper-convergente. Os anfitriões topo de gama têm 576 GB de RAM e processadores dual Intel 18, 2.3-GHz. Os anfitriões he têm dois grupos de discos vSAN com um total de 15,36 TB (SSD) de capacidade bruta vSAN e um nível de cache vSAN de 3,2 TB (NVMe).

Novas nuvens privadas são implantadas através do portal Azure ou Azure CLI.

## <a name="networking"></a>Redes

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Para obter mais informações sobre networking e interconectividade, consulte o artigo [conceitos de Networking.](concepts-networking.md)

## <a name="access-and-security"></a>Acesso e segurança

Para uma maior segurança, as nuvens privadas Azure VMware Solution utilizam o controlo de acesso baseado em funções vSphere. vSphere SSO LDAP pode ser integrado com Azure Ative Directory. Para mais informações sobre identidade e privilégios, consulte o artigo [Conceitos de Acesso e Identidade.](concepts-identity.md)

vSAN encriptação de dados em repouso é ativada por padrão e é usada para fornecer segurança vSAN datastore. É descrito com mais detalhes no artigo [de conceitos de armazenamento.](concepts-storage.md)

## <a name="host-and-software-lifecycle-maintenance"></a>Manutenção do ciclo de vida do hospedeiro e do software

Atualizações regulares do software Azure VMware Solution e software VMware garantem que os mais recentes conjuntos de segurança, estabilidade e funcionalidades estão a ser executadas nas suas nuvens privadas. Mais detalhes sobre manutenção da plataforma e atualizações estão disponíveis no artigo [de atualização de conceitos.](concepts-upgrades.md)

## <a name="monitoring-your-private-cloud"></a>Monitorização da sua nuvem privada

Assim que a Solução VMware Azure é implantada na sua subscrição, [os registos do Azure Monitor](../azure-monitor/overview.md) são gerados automaticamente. Além disso, pode recolher registos em cada uma das suas máquinas virtuais dentro da sua nuvem privada. Pode [descarregar e instalar o agente MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options) em máquinas virtuais Linux e Windows que estão a funcionar nas suas nuvens privadas Azure VMware Solution, bem como ativar a extensão de [diagnóstico Azure](../azure-monitor/platform/diagnostics-extension-overview.md). Pode até executar as mesmas consultas que normalmente corre nas suas máquinas virtuais. Para saber mais sobre a criação de consultas, consulte [a visão geral dos Registos do Monitor Azure](../azure-monitor/platform/data-platform-logs.md). Os padrões de monitorização dentro da Solução VMware Azure são semelhantes às Máquinas Virtuais Azure dentro da plataforma IaaS. Para obter informações adicionais e como fazer, consulte [as máquinas virtuais monitor de Azure com o Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender os principais [conceitos de nuvem privada e cluster.](concepts-private-clouds-clusters.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
