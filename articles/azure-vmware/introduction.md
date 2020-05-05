---
title: Introdução
description: Aprenda as funcionalidades e benefícios da Azure VMware Solution (AVS) para implementar e gerir cargas de trabalho baseadas em VMware no Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: e227b6c7c2f25fbf62f11c17dc62293e4fbd5267
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740333"
---
# <a name="what-is-azure-vmware-solution-avs-preview"></a>O que é a Solução Azure VMware (AVS) Pré-visualização?

A Azure VMware Solution (AVS) fornece-lhe nuvens privadas em Azure. As nuvens privadas contêm aglomerados vSphere, construídos a partir de infraestruturas azure de metal nu dedicado. Você pode escalar aglomerados de nuvem privada de 3 a 16 anfitriões, com a capacidade de ter vários clusters em uma única nuvem privada. Todas as nuvens privadas são aprovisionadas com vCenter Server, VSAN, vSphere e NSX-T. Pode migrar cargas de trabalho dos seus ambientes no local, criar ou implantar novas máquinas virtuais e consumir serviços Azure das suas nuvens privadas.

O AVS é uma solução validada por VMware com validação em curso e testes de melhorias e melhorias. A infraestrutura e software privados em nuvem são geridos e mantidos pela Microsoft, permitindo-lhe concentrar-se no desenvolvimento e funcionamento de cargas de trabalho nas suas nuvens privadas.

O diagrama seguinte mostra a adjacência entre nuvens privadas e VNets em ambientes Azure, Azure e no local. O acesso em rede de nuvens privadas a serviços Azure ou VNets proporciona a integração orientada pela SLA de pontos finais de serviço Azure. O acesso privado em nuvem a partir de ambientes no local utiliza o ExpressRoute Global Reach para uma ligação privada e segura.

![Imagem de adjacência de nuvem privada AVS para Azure e no local](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

Nuvens e aglomerados privados da AVS são construídos a partir de um hospedeiro de infraestruturas Azure hiper-convergente e de metal nu. Os anfitriões topo de gama têm 576 GB de RAM e processadores intel 18 duplos, 2,3 GHz. Os anfitriões he têm dois grupos de discos vSAN com um nível de capacidade vSAN cru de 15,36 TB (SSD) e um nível de cache vSAN de 3,2 TB (NVMe).

Novas nuvens privadas são implantadas através do portal Azure ou Azure CLI.

## <a name="networking"></a>Redes

Quando uma nuvem privada é implantada, são criadas redes privadas de gestão, provisionamento e vMotion. Estas redes privadas são usadas para acesso ao vCenter e ao NSX-T Manager, e para máquina virtual vMotion ou implementação. Todas as redes privadas são acessíveis a partir de uma VNet em Azure ou de ambientes no local. O ExpressRoute Global Reach é usado para ligar nuvens privadas a ambientes no local, e esta ligação requer um VNet com um circuito ExpressRoute na sua subscrição.

O acesso à internet e aos serviços Azure são prestados quando uma nuvem privada é implantada. O acesso é fornecido para que os VMs nas redes de carga de trabalho de produção possam consumir serviços Azure ou baseados na Internet. O acesso à Internet é desativado por padrão para novas nuvens privadas, e pode ser ativado ou desativado a qualquer momento.

Para obter mais informações sobre networking e interconectividade, consulte o artigo Conceitos de [Networking.](concepts-networking.md)

## <a name="access-and-security"></a>Acesso e segurança

Para uma maior segurança, as nuvens privadas AVS utilizam o controlo de acesso baseado em funções vSphere. as capacidades lDAP vSphere SSO podem ser integradas com o Diretório Ativo Azure. Para obter mais informações sobre identidade e [privilégios,](concepts-identity.md) consulte o artigo Conceitos de Acesso e Identidade.

a encriptação de dados vSAN em repouso é ativada por padrão e é usada para fornecer segurança vSAN datastore. É descrito mais detalhadamente no artigo dos conceitos de [armazenamento.](concepts-storage.md)

## <a name="host-and-software-lifecycle-maintenance"></a>Manutenção de ciclo de vida de hospedeiro e software

As atualizações regulares do software de nuvem privada EVware e Nuvem AVS garantem que os mais recentes conjuntos de segurança, estabilidade e funcionalidades estão a ser recorridos nas suas nuvens privadas. Mais detalhes sobre a manutenção da plataforma e atualizações estão disponíveis no artigo conceitos de [upgrade.](concepts-upgrades.md)

## <a name="monitoring-your-private-cloud"></a>Monitorizando a sua nuvem privada

Pode utilizar [registos no Monitor Azure](../azure-monitor/overview.md) para recolher registos nas suas máquinas virtuais em funcionamento na sua nuvem privada AVS. Pode [descarregar e instalar o agente MMA](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) nas máquinas virtuais do Linux e do Windows que funcionam nas suas nuvens privadas AVS, utilizando as mesmas consultas que executa nos seus VMs no local. Pode fazer as mesmas consultas que normalmente faria nas suas máquinas virtuais da mesma forma. Para saber mais sobre a criação de consultas, veja [como escrever consultas.](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries)

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender os principais [conceitos privados](concepts-private-clouds-clusters.md)de nuvem privada e cluster.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
