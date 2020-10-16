---
title: Conceitos - Nuvens privadas e aglomerados
description: Conheça as principais capacidades dos centros de dados definidos por software Azure VMware e dos clusters vSphere em VMware Solution on Azure by VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 715293d9951876ff0f794f8f6b580093f89571b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316873"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution conceitos de nuvem privada e cluster

A Azure VMware Solution oferece nuvens privadas baseadas em VMware em Azure. As nuvens privadas são construídas a partir de aglomerados de hospedeiros dedicados de metal nu e são implantadas e geridas através do portal Azure. Os clusters em nuvens privadas são a provisionados com software VMware vSphere, vCenter, vSAN e NSX. Azure VMware Solution hardware e software privados em nuvem são totalmente integrados e automatizados em Azure.

Há uma relação lógica entre subscrições Azure, nuvens privadas Azure VMware Solution, clusters vSAN e anfitriões. No diagrama, são mostradas duas nuvens privadas numa única subscrição do Azure. Nuvens privadas representam um desenvolvimento e um ambiente de produção, cada um com a sua própria nuvem privada. Em cada uma dessas nuvens privadas, há dois aglomerados. Para mostrar as necessidades potenciais mais baixas de um ambiente de desenvolvimento, são utilizados aglomerados menores com hospedeiros de menor capacidade. Todos estes conceitos são descritos nas secções abaixo.

![Imagem de duas nuvens privadas em uma subscrição de cliente](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Nuvens privadas

As nuvens privadas contêm aglomerados vSAN que são construídos com hospedeiros Azure dedicados e de metal nu. Cada nuvem privada pode ter vários clusters, todos geridos pelo mesmo servidor vCenter e gestor NSX-T. Pode implementar e gerir nuvens privadas no portal, a partir do CLI, ou com o PowerShell. Tal como acontece com outros recursos, as nuvens privadas são instaladas e geridas a partir de uma subscrição do Azure.

O número de nuvens privadas dentro de uma subscrição é escalável. Inicialmente, há um limite de uma nuvem privada por subscrição.

## <a name="clusters"></a>Clusters

Criará pelo menos um aglomerado vSAN em cada nuvem privada. Quando se cria uma nuvem privada, há um aglomerado por defeito. Pode adicionar aglomerados adicionais a uma nuvem privada utilizando o portal Azure ou através da API. Todos os clusters têm um tamanho padrão de três anfitriões e podem ser dimensionados de 3 a 16 anfitriões. O tipo de hospedeiros utilizados num aglomerado deve ser do mesmo tipo. Os tipos de anfitriões são descritos na secção seguinte.

Os clusters de ensaio estão disponíveis para avaliação e estão limitados a três anfitriões e um único cluster de ensaios por nuvem privada. Pode escalar um cluster de ensaio por um único hospedeiro durante o período de avaliação.

Cria, apaga e escala os clusters através do portal ou da API. Você ainda usa vSphere e NSX-T Manager para gerir a maioria dos outros aspetos da configuração ou operação do cluster. Todo o armazenamento local de cada hospedeiro em um cluster está sob o controlo de vSAN.

## <a name="hosts"></a>Anfitriões

Os nós de infraestrutura de metal hiper-convergente são utilizados em aglomerados de nuvem privada Azure VMware Solution. As capacidades de RAM, CPU e disco do anfitrião estão fornecidas na tabela abaixo. 

| Tipo de Anfitrião              |             CPU             |   RAM (GB)   |  vSAN NVMe cache Tier (TB, cru)  |  vSAN SSD nível de capacidade (TB, cru)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  dual Intel 18 core 2.3 GHz  |     576      |                3.2               |                15.20               |

Os anfitriões que são usados para construir ou escalar clusters são adquiridos a partir de uma piscina isolada de anfitriões. Estes anfitriões passaram em testes de hardware e tiveram todos os dados eliminados de forma segura dos discos flash. Quando remove um hospedeiro de um cluster, os discos internos são limpos de forma segura e os anfitriões são colocados na piscina isolada de anfitriões. Quando se adiciona um hospedeiro a um cluster, é utilizado um hospedeiro higienizado da piscina isolada.

## <a name="vmware-software-versions"></a>Versões de software VMware

As versões de software atuais do software VMware utilizado nos clusters de nuvem privada Azure VMware Solution são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U2    | 
| ESXi                  |    6.7 U2    | 
| vSAN                  |    6.7 U2    |
| NSX-T                 |      2.5     |

Para qualquer novo cluster numa nuvem privada, a versão do software irá corresponder ao que está atualmente a ser em execução na nuvem privada. Para qualquer nova nuvem privada numa subscrição de cliente, a versão mais recente da pilha de software está instalada.

As políticas e processos gerais de upgrade para o software da plataforma Azure VMware Solution estão descritos no documento De Upgrades Concepts.

## <a name="host-maintenance-and-lifecycle-management"></a>Manutenção do anfitrião e gestão do ciclo de vida

A manutenção do hospedeiro e a gestão do ciclo de vida são feitas sem impacto na capacidade ou desempenho de aglomerados de nuvens privadas. Exemplos de manutenção automatizada do hospedeiro incluem upgrades de firmware e reparação de hardware ou substituição.

A Microsoft é responsável pela gestão do ciclo de vida de aparelhos NSX-T, tais como NSX-T Manager e NSX-T Edge. A Microsoft também é responsável pela config de rede de bootstrapping, como a criação do gateway Tier-0 e permitir North-South encaminhamento. Como administrador da sua nuvem privada Azure VMware Solution, é responsável pela configuração NSX-T SDN. Por exemplo, segmentos de rede, regras de firewall distribuídas, gateways de nível 1 e equilibres de carga.

> [!IMPORTANT]
> Um administrador de Solução VMware Azure não deve modificar a configuração de NSX-T Edge ou Gateway Tier-0. Isto pode resultar numa perda de serviço.

## <a name="backup-and-restoration"></a>Backup e restauro

As configurações de nuvem privada vCenter e NSX-T são apoiadas de hora em hora. Os reforços são mantidos por três dias. A restauração de uma cópia de segurança é solicitada através de um Pedido de Serviço no portal Azure.

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender [conceitos de networking e interconectividade.](concepts-networking.md)

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

