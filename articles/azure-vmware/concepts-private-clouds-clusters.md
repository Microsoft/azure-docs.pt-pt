---
title: Conceitos - Nuvens privadas e aglomerados
description: Conheça as principais capacidades dos centros de dados definidos por software Azure VMware Solution e clusters vSphere.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 0454ade503ce40de46363f65d36a64340219c0b1
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874020"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution conceitos de nuvem privada e cluster

A Azure VMware Solution oferece nuvens privadas baseadas em VMware em Azure. Nuvens privadas contêm aglomerados construídos com hospedeiros Azure dedicados e de metal nu. São implantados e geridos através do portal Azure, CLI ou PowerShell.  Os clusters a provisionados em nuvens privadas incluem vMware vSphere, vCenter, vSAN e software NSX. Azure VMware Solution hardware e software privados em nuvem são totalmente integrados e automatizados em Azure.

Há uma relação lógica entre subscrições Azure, nuvens privadas Azure VMware Solution, clusters vSAN e anfitriões. O diagrama mostra uma única assinatura Azure com duas nuvens privadas que representam o ambiente de desenvolvimento e produção.  Em cada uma dessas nuvens privadas há dois aglomerados. 

Este artigo descreve todos estes conceitos.

![Imagem de duas nuvens privadas em uma subscrição de cliente](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>Devido às necessidades potenciais mais baixas de um ambiente de desenvolvimento, utilize clusters menores com hospedeiros de menor capacidade. 

## <a name="private-clouds"></a>Nuvens privadas

Nuvens privadas contêm aglomerados vSAN construídos com hospedeiros Azure dedicados e de metal nu. Cada nuvem privada pode ter vários clusters geridos pelo mesmo servidor vCenter e gestor NSX-T. Pode implementar e gerir nuvens privadas no portal, CLI ou PowerShell. 

Tal como acontece com outros recursos, as nuvens privadas são instaladas e geridas a partir de uma subscrição do Azure. O número de nuvens privadas dentro de uma subscrição é escalável. Inicialmente, há um limite de uma nuvem privada por subscrição.

## <a name="clusters"></a>Clusters
Para cada nuvem privada criada, há um cluster vSAN por padrão. Pode adicionar, eliminar e escalar agrupamentos utilizando o portal Azure ou através da API.  Todos os clusters têm um tamanho padrão de três anfitriões e podem escalar até 16 anfitriões.  Os anfitriões utilizados num aglomerado devem ser do mesmo tipo de hospedeiro.

Os clusters de ensaio estão disponíveis para avaliação e limitados a três anfitriões. Há um único aglomerado de ensaios por nuvem privada. Pode escalar um cluster de ensaio por um único hospedeiro durante o período de avaliação.

Você usa vSphere e NSX-T Manager para gerir a maioria dos outros aspetos da configuração ou funcionamento do cluster. Todo o armazenamento local de cada hospedeiro em um cluster está sob o controlo de vSAN.

## <a name="hosts"></a>Anfitriões

Azure VMware Solution os aglomerados de nuvem privada utilizam nós de infraestrutura de metal hiperconvernado e desnudado. A tabela seguinte mostra as capacidades de RAM, CPU e disco do hospedeiro. 

| Tipo de Anfitrião              |             CPU             |   RAM (GB)   |  vSAN NVMe cache Tier (TB, cru)  |  vSAN SSD nível de capacidade (TB, cru)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  dual Intel 18 core 2.3 GHz  |     576      |                3.2               |                15.20               |

Os anfitriões usados para construir ou escalar aglomerados vêm de uma piscina isolada de anfitriões. Estes anfitriões passaram em testes de hardware e tiveram todos os dados eliminados de forma segura. 

## <a name="vmware-software-versions"></a>Versões de software VMware

As versões de software atuais do software VMware utilizado nos clusters de nuvem privada Azure VMware Solution são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |

Para qualquer novo cluster numa nuvem privada, a versão de software corresponde ao que está atualmente em execução. Para qualquer nova nuvem privada numa subscrição, a versão mais recente da stack de software é instalada.

Pode encontrar as políticas e processos gerais de upgrade para o software da plataforma Azure VMware Solution descrito em [atualizações e atualizações de nuvem privada .](concepts-upgrades.md)

## <a name="host-maintenance-and-lifecycle-management"></a>Manutenção do anfitrião e gestão do ciclo de vida

A manutenção do hospedeiro e a gestão do ciclo de vida não têm qualquer impacto na capacidade ou desempenho dos aglomerados de nuvem privada.  Exemplos de manutenção automatizada do hospedeiro incluem upgrades de firmware e reparação de hardware ou substituição.

A Microsoft é responsável pela gestão do ciclo de vida de aparelhos NSX-T, tais como NSX-T Manager e NSX-T Edge. São também responsáveis pela configuração da rede de bootstrapping, como a criação do gateway Tier-0 e a viação North-South. É responsável pela configuração NSX-T SDN. Por exemplo, segmentos de rede, regras de firewall distribuídas, gateways de nível 1 e equilibres de carga.

> [!IMPORTANT]
> Não modifique a configuração de NSX-T Edge ou Gateway Tier-0, pois isto pode resultar numa perda de serviço.

## <a name="backup-and-restoration"></a>Backup e restauro

As configurações de nuvem privada vCenter e NSX-T estão num horário de backup de hora a hora.  Os reforços são mantidos por três dias. Se precisar de restaurar a partir de uma cópia de segurança, abra um pedido de [apoio](https://rc.portal.azure.com/#create/Microsoft.Support) no portal Azure para solicitar a restauração.

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender [conceitos de networking e interconectividade.](concepts-networking.md)

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

