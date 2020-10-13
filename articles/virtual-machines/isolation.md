---
title: Isolamento para VMs em Azure
description: Saiba mais sobre o isolamento VM funciona em Azure.
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/18/2019
ms.author: sttsinar
ms.openlocfilehash: d6a77cac049ef32949eeba4cbd6fe9217100a360
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666845"
---
# <a name="virtual-machine-isolation-in-azure"></a>Isolamento de máquina virtual em Azure

O Azure Compute oferece tamanhos de máquinas virtuais que são isolados a um tipo de hardware específico e dedicados a um único cliente. Os tamanhos isolados vivem e operam na geração específica de hardware e serão depreciados quando a geração de hardware for reformada.

Os tamanhos de máquinas virtuais isolados são mais adequados para cargas de trabalho que requerem um alto grau de isolamento das cargas de trabalho de outros clientes por razões que incluem o cumprimento dos requisitos de conformidade e regulamentação.  Utilizar um tamanho isolado garante que a sua máquina virtual será a única a funcionar nessa instância específica do servidor. 


Além disso, uma vez que os VMs de tamanho isolado são grandes, os clientes podem optar por subdividir os recursos destes VMs utilizando [suporte Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

As atuais ofertas de máquinas virtuais isoladas incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> Os tamanhos VM isolados têm um tempo de vida limitado de hardware. Veja abaixo os detalhes

## <a name="deprecation-of-isolated-vm-sizes"></a>Depreciação de tamanhos VM isolados
Como os tamanhos de VM isolados são tamanhos ligados ao hardware, o Azure fornecerá lembretes 12 meses antes da depreciação oficial dos tamanhos.  O Azure também irá oferecer um tamanho isolado atualizado na nossa próxima versão de hardware que o cliente poderia considerar mover a sua carga de trabalho para.

| Tamanho | Data de aposentadoria de isolamento | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15 de maio de 2020 |
| Standard_D15_v2<sup>1</sup>  | 15 de maio de 2020 |

<sup>1</sup>  Para mais detalhes sobre Standard_DS15_v2 e Standard_D15_v2 programa de aposentadoria de isolamento ver FAQs


## <a name="faq"></a>FAQ
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>P: O tamanho vai ficar reformado ou só é a característica de "isolamento"?
**R**: Se o tamanho da máquina virtual não tiver o subscrito "i", apenas a função "isolamento" será retirada. Se não for necessário um isolamento, não haverá qualquer ação a tomar e o VM continuará a funcionar como esperado. Exemplos incluem Standard_DS15_v2, Standard_D15_v2, Standard_M128ms etc. Se o tamanho da máquina virtual incluir o subscrito "i", então o tamanho vai ser retirado.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: Há algum tempo de paragem quando o meu vm aterra num hardware não isolado?
**R**: Se não houver necessidade de isolamento, não são necessárias medidas e não haverá tempo de inatividade.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>P: Existe algum custo delta para se mudar para uma máquina virtual não isolada?
**A:** Não

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Quando é que os outros tamanhos isolados se vão reformar?
**R**: Forneceremos lembretes com 12 meses de antecedência da depreciação oficial do tamanho isolado.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: Sou um cliente de tecido de serviço Azure que conta com os níveis de durabilidade prateada ou dourada. Esta mudança tem impacto em mim?
**A:** Não. As garantias fornecidas pelos Tiers de [Durabilidade](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) da Service Fabric continuarão a funcionar mesmo após esta alteração. Se necessitar de isolamento físico de hardware por outras razões, poderá ainda ter de tomar uma das ações descritas acima. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>P: Quais são os marcos para a D15_v2 ou DS15_v2 a reforma do isolamento? 
**A:** 
 
| Date | Ação |
|---|---| 
| 18 de novembro de 2019 | Disponibilidade de D/DS15i_v2 (PAYG, 1 ano RI) | 
| 14 de maio de 2020 | Último dia para comprar D/DS15i_v2 1 ano RI | 
| 15 de maio de 2020 | Garantia de isolamento D/DS15_v2 removida | 
| 15 de maio de 2021 | Aposentar-se D/DS15i_v2 (todos os clientes exceto quem comprou RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019)| 
| 17 de novembro de 2022 | Aposentar-se D/DS15i_v2 quando 3 anos de RIs feito (para clientes que compraram RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019) |

## <a name="next-steps"></a>Passos seguintes

Os clientes também podem optar por subdividir ainda mais os recursos destas máquinas virtuais isoladas utilizando [o suporte Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
