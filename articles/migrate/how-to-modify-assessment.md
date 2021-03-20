---
title: Personalizar avaliações para a avaliação do servidor Azure Migrate | Microsoft Docs
description: Descreve como personalizar avaliações criadas com a avaliação do servidor Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: de90cffe8760a55aafebf079678d139c6f6f99e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751109"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar avaliações criadas pela Azure Migrate Server Assessment.

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para Azure. O hub fornece ferramentas Azure Migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

Pode utilizar a ferramenta de avaliação do servidor Azure Migrate para criar avaliações para VMware VMs e VMs hiper-V, em preparação para a migração para Azure. A ferramenta de avaliação do servidor avalia os servidores no local para migração para máquinas virtuais Azure IaaS e Solução VMware Azure (AVS). 

## <a name="about-assessments"></a>Sobre avaliações

As avaliações que cria com a Avaliação do Servidor são uma imagem pontual dos dados. Existem dois tipos de avaliações que pode criar usando Azure Migrate: Avaliação do servidor.

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md)no local, [VMs hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para Azure utilizando este tipo de avaliação. (concepts-assessment-calculation.md)
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local para a migração para o Azure VMware Solution (AVS) com este tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

Uma avaliação Azure VM na Avaliação do Servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho recolhidos | **Avaliação da VM do Azure**: A recomendação de tamanho da VM baseia-se nos dados de utilização da CPU e da memória.<br/><br/> A recomendação do tipo de disco (HDD/SSD padrão ou discos geridos premium) baseia-se no IOPS e no débito dos discos no local.<br/><br/> **Avaliação do Azure VMware Solution (AVS)** : A recomendação de nós AVS baseia-se nos dados de utilização da CPU e da memória.
**Como está no local** | Avaliações que não utilizam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: A recomendação de tamanho da VM baseia-se no tamanho da VM no local<br/><br> O tipo de disco recomendado baseia-se naquilo que seleciona na definição do tipo de armazenamento para a avaliação.<br/><br/> **Avaliação do Azure VMware Solution (AVS)** : A recomendação de nós AVS baseia-se no tamanho da VM no local.


## <a name="how-is-an-assessment-done"></a>Como se faz uma avaliação?

Uma avaliação feita na Avaliação do Servidor Azure Migrate tem três fases. A avaliação começa com uma análise de adequação, seguida de dimensionamento e, por último, uma estimativa mensal de custos. Uma máquina só se move para uma fase posterior se passar a anterior. Por exemplo, se uma máquina falhar a verificação de adequação do Azure, está marcada como inadequada para o Azure, e o tamanho e o custo não serão feitos. [Saiba mais.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>O que há numa avaliação do Azure VM?

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A localização do Azure para a qual pretende migrar.<br/> Atualmente, a Avaliação de Servidores apoia estas regiões-alvo: Austrália Oriental, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Eua Central, China Leste, China Norte, Ásia Oriental, Leste DOS EUA, Alemanha Central, Alemanha Nordeste, Japão Oriental, Japão Oeste, Coreia Central, Coreia do Sul, Eua Central Norte, Europa do Norte, Centro Sul da Ásia, Sudeste da Índia, Reino Unido, Reino Unido , EUA Gov Texas, US Gov Virginia, West Central US, West Europe, West India, West US, e West US2.
**Tipo de armazenamento** | Pode utilizar esta propriedade para especificar o tipo de discos para onde pretende mover-se, em Azure.<br/><br/> Para o tamanho das instalações, pode especificar o tipo de armazenamento alvo quer como discos geridos por Premium, discos geridos por SSD standard ou discos geridos por HDD standard. Para dimensionamento baseado no desempenho, pode especificar o tipo de disco-alvo como discos automáticos, geridos por Premium, discos geridos por HDD padrão ou discos geridos por SSD standard.<br/><br/> Quando especifica o tipo de armazenamento como automático, a recomendação do disco é feita com base nos dados de desempenho dos discos (IOPS e produção). Se especificar o tipo de armazenamento como premium/padrão, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se pretender obter um SLA VM de 99,9%, poderá querer especificar o tipo de armazenamento como discos geridos por Premium. Isto garante que todos os discos da avaliação são recomendados como discos geridos por Premium. Azure
**Instâncias Reservadas (RI)** | Esta propriedade ajuda-o a especificar se tem [Instâncias Reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) em Azure, as estimativas de custos na avaliação são feitas então tendo em descontos RI. Atualmente, os casos reservados são apenas suportados para a oferta Pay-As-You-Go em Azure Migrate.
**Critério de dimensionamento** | O critério a ser usado para VMs de tamanho certo para Azure. Você pode fazer dimensionamento *baseado no desempenho* ou tamanho os VMs como no *local*, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a ter em conta para a avaliação dos dados de desempenho das máquinas. Esta propriedade só é aplicável quando o critério de dimensionamento é *baseado no desempenho.*
**Utilização de percentil** | O valor de percentil da amostra de desempenho definido para ser considerado para o dimensionamento certo. Esta propriedade só é aplicável quando o dimensionamento é *baseado no desempenho.*
**Série das VMs** |     Pode especificar a série das VMs que gostaria de considerar para redimensionamento. Por exemplo, se tiver um ambiente de produção que não planeia migrar para VMs da série A em Azure, pode excluir sérieS A da lista ou série e o tamanho certo é feito apenas na série selecionada.
**Fator de conforto** | A avaliação do servidor Azure Migrate considera um tampão (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está escrito. O Azure Migrate calcula o custo em conformidade.
**Moeda** | A moeda de faturação.
**Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure.<br/> A predefinição é 0%.
**Tempo de atividade de VM** | Se os seus VMs não estiverem a funcionar 24x7 em Azure, pode especificar a duração (número de dias por mês e número de horas por dia) para a qual estariam a funcionar e as estimativas de custos seriam feitas em conformidade.<br/> O valor predefinido é de 31 dias por mês e de 24 horas por dia.
**Benefício Híbrido do Azure** | Especifique se tem garantia de software e é elegível para [benefício híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. A predefinição é Sim.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>O que está numa avaliação da Solução VMware Azure (AVS) ?

Aqui está o que está incluído numa avaliação avs na avaliação do servidor:


| **Propriedade** | **Detalhes** |
| - | - |
| **Localização de destino** | Especifica a localização da nuvem privada AVS para a qual pretende migrar.<br/><br/> A avaliação do AVS na avaliação do servidor suporta atualmente estas regiões-alvo: Leste dos EUA, Europa Ocidental, EUA Ocidentais. |
| **Tipo de armazenamento** | Especifica o motor de armazenamento a utilizar em AVS.<br/><br/> Note que as avaliações avs apenas suportam vSAN como um tipo de armazenamento predefinido. |
**Instâncias Reservadas (RIs)** | Esta propriedade ajuda-o a especificar Instâncias Reservadas em AVS. Os RIs não são atualmente suportados para os nós AVS. |
**Tipo de nó** | Especifica o [tipo de nó AVS](../azure-vmware/concepts-private-clouds-clusters.md) utilizado para mapear os VMs no local. Note que o tipo de nó predefinido é AV36. <br/><br/> A Azure Migrate recomendará um número necessário de nós para que os VMs sejam migrados para AVS. |
**Definição FTT, nível raid** | Especifica as combinações de Falha de Tolerar e Raid aplicáveis. A opção FTT selecionada combinada com o requisito do disco VM no local determinará o armazenamento total de vSAN exigido em AVS. |
**Critério de dimensionamento** | Define os critérios a utilizar para VMs _de tamanho correto_ para AVS. Pode optar pelo dimensionamento _baseado no desempenho_ ou como no local sem considerar o histórico _de_ desempenho. |
**Histórico de desempenho** | Define a duração a ter em conta na avaliação dos dados de desempenho das máquinas. Esta propriedade só é aplicável quando os critérios de dimensionamento são _baseados no desempenho._ |
**Utilização de percentil** | Especifica o valor percentil do conjunto de amostras de desempenho a considerar para o tamanho certo. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho.|
**Fator de conforto** | A avaliação do servidor Azure Migrate considera um tampão (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos. |
**Oferta** | Exibe a oferta do [Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está matriculado. O Azure Migrate calcula o custo em conformidade.|
**Moeda** | Mostra a moeda de faturação da sua conta. |
**Desconto (%)** | Lista qualquer desconto específico por subscrição que receba em cima da oferta Azure. A predefinição é 0%. |
**Benefício Híbrido do Azure** | Especifica se tem garantia de software e é elegível para [benefício híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Embora isso não tenha qualquer impacto nos preços das soluções Azure VMware devido ao preço baseado no nó, os clientes ainda podem aplicar as suas licenças pré-SISTEMA (com base na Microsoft) em AVS usando benefícios híbridos Azure. Outros fornecedores de SISTEMA de software terão de fornecer os seus próprios termos de licenciamento, como o RHEL, por exemplo. |
**vCPU Oversubscription** | Especifica a relação de número de núcleos virtuais ligados a 1 núcleo físico no nó AVS. O valor predefinido nos cálculos é de 4 vCPU: 1 núcleo físico em AVS. <br/><br/> Os utilizadores da API podem definir este valor como um inteiro. Note que a subscrição excessiva do vCPU > 4:1 pode começar a causar degradação do desempenho, mas pode ser usada para cargas de trabalho do tipo web do servidor. |

## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar propriedades de avaliação após a criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate, clique em **Servidores**.
2. Em **Azure Migrate: Avaliação do servidor,** clique na contagem de avaliações.
3. Em **Avaliação,** clique na avaliação relevante > **Editar propriedades.**
5. Personalize as propriedades de avaliação de acordo com as tabelas acima.
6. Clique **em Guardar** para atualizar a avaliação.


Também pode editar propriedades de avaliação quando estiver a criar uma avaliação.


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações de Azure VM são calculadas.
- [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre como as avaliações do AVS são calculadas.
