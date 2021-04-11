---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: 83a19dea56693a1caff2c982b9f772543fe1cf2e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073589"
---
O Azure Compute oferece tamanhos de máquinas virtuais que são isolados a um tipo de hardware específico e dedicados a um único cliente. Os tamanhos isolados vivem e operam na geração específica de hardware e serão depreciados quando a geração de hardware for reformada.

Os tamanhos de máquinas virtuais isolados são mais adequados para cargas de trabalho que requerem um alto grau de isolamento das cargas de trabalho de outros clientes por razões que incluem o cumprimento dos requisitos de conformidade e regulamentação.  Utilizar um tamanho isolado garante que a sua máquina virtual será a única a funcionar nessa instância específica do servidor. 


Além disso, uma vez que os VMs de tamanho isolado são grandes, os clientes podem optar por subdividir os recursos destes VMs utilizando [suporte Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

As atuais ofertas de máquinas virtuais isoladas incluem:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DC8_v2


> [!NOTE]
> Os tamanhos VM isolados têm um tempo de vida limitado de hardware. Veja abaixo os detalhes

## <a name="deprecation-of-isolated-vm-sizes"></a>Depreciação de tamanhos VM isolados

Os tamanhos de VM isolados têm uma duração de vida limitada de hardware. O Azure emitirá lembretes com 12 meses de antecedência da data oficial de depreciação dos tamanhos e fornecerá uma oferta isolada atualizada para a sua consideração.

| Tamanho | Data de aposentadoria de isolamento | 
| --- | --- |
| Standard_DS15_v2 | 15 de maio de 2021 |
| Standard_D15_v2  | 15 de maio de 2021 |
| Standard_G5  | 15 de fevereiro de 2022 |
| Standard_GS5  | 15 de fevereiro de 2022 |
| Standard_E64i_v3  | 15 de fevereiro de 2022 |
| Standard_E64is_v3  | 15 de fevereiro de 2022 |
| Standard_DC8_v2 | 15 de fevereiro de 2022 |


## <a name="faq"></a>FAQ
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>P: O tamanho vai ser reformado ou apenas a sua característica de "isolamento"?
**R**: Atualmente, apenas a característica de isolamento dos tamanhos VM está a ser retirada. Os tamanhos isolados precotados continuarão a existir em estado não isolado. Se não for necessário um isolamento, não haverá qualquer ação a tomar e o VM continuará a funcionar como esperado.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: Há algum tempo de paragem quando o meu vm aterra num hardware não isolado?
**R**: Se não houver necessidade de isolamento, não são necessárias medidas e não haverá tempo de inatividade. Pelo contrário, se for necessário um isolamento, o nosso anúncio incluirá o tamanho de substituição recomendado. A seleção do tamanho de substituição exigirá que os nossos clientes redimensionem os seus VMs.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>P: Existe algum custo delta para se mudar para uma máquina virtual não isolada?
**A:** Não

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Quando é que os outros tamanhos isolados se vão reformar?
**R**: Forneceremos lembretes com 12 meses de antecedência da depreciação oficial do tamanho isolado. O nosso último anúncio inclui a retirada de Standard_G5, Standard_GS5, Standard_E64i_v3 e Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: Sou um cliente de tecido de serviço Azure que conta com os níveis de durabilidade prateada ou dourada. Esta mudança tem impacto em mim?
**A:** Não. As garantias fornecidas pelos Tiers de [Durabilidade](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) da Service Fabric continuarão a funcionar mesmo após esta alteração. Se necessitar de isolamento físico de hardware por outras razões, poderá ainda ter de tomar uma das ações descritas acima. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>P: Quais são os marcos para a D15_v2 ou DS15_v2 a reforma do isolamento? 
**A:** 
 
| Data | Ação |
|---|---| 
| 15 de maio de 2020<sup>1</sup> | Anúncio de aposentadoria de isolamento D/DS15_v2| 
| 15 de maio de 2021 | Garantia de isolamento D/DS15_v2 removida| 

<sup>1</sup> O cliente existente que utilize estes tamanhos receberá um e-mail de anúncio com instruções detalhadas sobre os próximos passos.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>P: Quais são os marcos para a reforma do G5, Gs5, E64i_v3 e E64is_v3? 
**A:** 
 
| Data | Ação |
|---|---|
| Fev 15, 2021<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 anúncio de reforma de isolamento |
| 28 de fevereiro de 2022 | Garantia de isolamento G5/GS5/E64i_v3/E64is_v3 removida |

<sup>1</sup> O cliente existente que utilize estes tamanhos receberá um e-mail de anúncio com instruções detalhadas sobre os próximos passos.  

## <a name="next-steps"></a>Passos seguintes

Os clientes também podem optar por subdividir ainda mais os recursos destas máquinas virtuais isoladas utilizando [o suporte Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
