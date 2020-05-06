---
title: Conceitos - Nuvens privadas e aglomerados
description: Conheça as principais capacidades dos centros de dados definidos por software Azure VMware e dos clusters vSphere em VMware Solution on Azure by VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: f51b76b654a43d690aa5c97fa9df99fad1f47d7a
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740385"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Solução Azure VMware (AVS) Pré-visualizar conceitos privados de nuvem e cluster

A Azure VMware Solution (AVS) oferece nuvens privadas baseadas em VMware em Azure. As nuvens privadas são construídas a partir de aglomerados de anfitriões dedicados de metal nu e são implantadas e geridas através do portal Azure. Os clusters em nuvens privadas são aprovisionados com vSphere vSphere, vCenter, vSAN e nsx software. As implementações privadas de hardware e software em nuvem aVS estão totalmente integradas e automatizadas no Azure.

Há uma relação lógica entre assinaturas Azure, nuvens privadas AVS, aglomerados vSAN e anfitriões. No diagrama, são mostradas duas nuvens privadas numa única subscrição azure. As nuvens privadas representam um desenvolvimento e um ambiente de produção, cada um com a sua própria nuvem privada. Em cada uma dessas nuvens privadas há dois aglomerados. Para mostrar as menores necessidades potenciais de um ambiente de desenvolvimento, são utilizados aglomerados menores com hospedeiros de menor capacidade. Todos estes conceitos são descritos nas secções abaixo.

![Imagem de duas nuvens privadas em uma subscrição de cliente](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Nuvens privadas

Nuvens privadas contêm aglomerados vSAN que são construídos com anfitriões dedicados e de metal azure. Cada nuvem privada pode ter vários clusters, todos geridos pelo mesmo servidor vCenter, e gestor NSX-T. Pode implantar e gerir nuvens privadas no portal, a partir do CLI, ou com powerShell. Tal como acontece com outros recursos, as nuvens privadas são instaladas e geridas a partir de uma subscrição do Azure.

O número de nuvens privadas dentro de uma subscrição é escalável. Inicialmente, há um limite de uma nuvem privada por subscrição.

## <a name="clusters"></a>Clusters

Criará pelo menos um aglomerado vSAN em cada nuvem privada. Quando se cria uma nuvem privada, há um aglomerado por defeito. Pode adicionar aglomerados adicionais a uma nuvem privada utilizando o portal Azure ou através da API. Todos os clusters têm um tamanho padrão de três anfitriões e podem ser dimensionados de 3 a 16 anfitriões. O tipo de hospedeiros utilizados num aglomerado deve ser do mesmo tipo. Os anfitriões são descritos na secção seguinte.

Os agrupamentos de ensaios estão disponíveis para avaliação e estão limitados a três anfitriões e a um único aglomerado de ensaios por nuvem privada. Pode escalar um cluster de ensaio sumido por um único hospedeiro durante o período de avaliação.

Cria, elimina e escala aglomerados através do portal ou DaPI. Ainda utiliza o vSphere e o NSX-T Manager para gerir a maioria dos outros aspetos da configuração ou operação do cluster. Todo o armazenamento local de cada hospedeiro num aglomerado está sob o controlo da vSAN.

## <a name="hosts"></a>Anfitriões

Os nós de infraestrutura de metal hiper-convergido são usados em aglomerados de nuvens privadas AVS. As capacidades ram, CPU e disco do hospedeiro são fornecidas na tabela abaixo. 

| Tipo de Anfitrião              |             CPU             |   RAM (GB)   |  vSAN NVMe cache Tier (TB, cru)  |  vSAN SSD nível de capacidade (TB, cru)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  dupla Intel 18 core 2.3 GHz  |     576      |                3,2               |                15.20               |

Os anfitriões que são usados para construir ou escalar aglomerados são adquiridos a partir de uma piscina isolada de anfitriões. Estes anfitriões passaram em testes de hardware e tiveram todos os dados eliminados de forma segura dos discos flash. Quando se retira um hospedeiro de um aglomerado, os discos internos são limpos de forma segura e os anfitriões são colocados na piscina isolada de anfitriões. Quando você adiciona um hospedeiro a um aglomerado, um hospedeiro desanitado da piscina isolada é usado.

## <a name="vmware-software-versions"></a>Versões de software VMware

As versões atuais do software VMware utilizadas em clusters de nuvem privadaS AVS são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U2    | 
| ESXi                  |    6.7 U2    | 
| vSAN                  |    6.7 U2    |
| NSX-T                 |      2.5     |

Para qualquer novo cluster numa nuvem privada, a versão do software corresponderá ao que está atualmente a ser gerido na nuvem privada. Para qualquer nova nuvem privada numa subscrição de cliente, a versão mais recente da pilha de software está instalada.

As políticas e processos gerais de atualização para o software da plataforma AVS são descritos no documento Upgrades Concepts.

## <a name="host-maintenance-and-lifecycle-management"></a>Gestão de manutenção e ciclo de vida

A manutenção do hospedeiro e a gestão do ciclo de vida são feitas sem impacto na capacidade ou desempenho de aglomerados privados de nuvem. Exemplos de manutenção automatizada do hospedeiro incluem atualizações de firmware e reparação ou substituição de hardware.

A Microsoft é responsável pela gestão do ciclo de vida de aparelhos NSX-T, tais como NSX-T Manager e NSX-T Edges. A Microsoft também é responsável pela utilização da rede de encaminhamento, como a criação do gateway Tier-0 e a habilitação do Routing Norte-Sul. Como administrador da sua nuvem privada AVS, é responsável pela configuração nsx-T SDN como segmentos de rede, regras de firewall distribuídas, gateways de nível 1 e equilibradores de carga.

> [!IMPORTANT]
> Um administrador AVS não deve modificar a configuração de NSX-T Edges ou Gateway Tier-0. Isto pode resultar numa perda de serviço.

## <a name="backup-and-restoration"></a>Backup e restauro

As configurações privadas de vCenter e NSX-T estão apoiadas de hora a hora. Os reforços são mantidos por três dias. A restauração de um backup é solicitada através de um Pedido de Serviço no portal Azure.

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender conceitos de [networking e interconectividade.](concepts-networking.md)

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

