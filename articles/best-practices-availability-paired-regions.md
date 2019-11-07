---
title: Continuidade dos negócios & recuperação de desastre-regiões emparelhadas do Azure
description: Saiba mais sobre o emparelhamento regional do Azure para garantir que os aplicativos sejam resilientes durante falhas de data center.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: b71048412f5715fd1b8ef3edf742716916672bd5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718743"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade dos negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma geografia, que contém um ou mais data centers.

Cada região do Azure é emparelhada com outra região na mesma geografia, juntos criando um par regional. A exceção é sul do Brasil, que é emparelhada com uma região fora de sua geografia. Nos pares de região, o Azure serializa as atualizações de plataforma (manutenção planejada), para que apenas uma região emparelhada seja atualizada de cada vez. No caso de uma interrupção que afete várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – pares regionais do Azure

| Gráfico | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| Ásia |Ásia Oriental |Sudeste Asiático |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |EUA Centro-Sul |
| Canadá |Canadá Central |Leste do Canadá |
| China |China Norte |Norte da China|
| China |Norte da China 2 |Leste da China 2|
| Europa |Europa do Norte (Irlanda) |Europa Ocidental (Países Baixos) |
| França |França Central|Sul de França|
| Alemanha |Alemanha Central |Alemanha Nordeste |
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia do Sul Central |Coreia do Sul |
| América do Norte |EUA Leste |Oeste dos E.U.A. |
| América do Norte |EUA Leste 2 |EUA Central |
| América do Norte |E.U.A. Centro-Norte |EUA Centro-Sul |
| América do Norte |EUA Oeste 2 |EUA Centro-Oeste 
| África do Sul | Norte da África do Sul | Oeste da África do Sul
| RU |Reino Unido Oeste |Sul do Reino Unido |
| Emirados Árabes Unidos | Norte dos E.A.U. | E.A.U. Central
| Departamento de defesa dos EUA |US DoD Leste |US DoD Centro |
| Governo dos Estados Unidos da América |Gov (US) - Arizona |Gov (US) - Texas |
| Governo dos Estados Unidos da América |US Gov - Iowa |Gov (US) - Virginia |
| Governo dos Estados Unidos da América |Gov (US) - Virginia |Gov (US) - Texas |

Tabela 1-mapeamento de pares regionais do Azure

- A Índia ocidental é emparelhada apenas em uma direção. A região secundária da Índia ocidental é sul da Índia, mas a região secundária do Sul da Índia é a Índia central.
- O sul do Brasil é exclusivo porque está emparelhado com uma região fora de sua própria geografia. A região secundária do Sul do Brasil é EUA Central do Sul. A região secundária do Sul EUA Central não é sul do Brasil.
- A região secundária do US Gov Iowa é US Gov-Virgínia.
- A região secundária do US Gov-Virgínia é US Gov Texas.
- A região secundária do US Gov Texas é US Gov Arizona.


Recomendamos que você configure a BCDR (recuperação de desastre de continuidade de negócios) em pares regionais para se beneficiar das políticas de isolamento e disponibilidade do Azure. Para aplicativos que dão suporte a várias regiões ativas, é recomendável usar ambas as regiões em um par de regiões sempre que possível. Isso garantirá a disponibilidade ideal para aplicativos e o tempo de recuperação minimizado em caso de desastre. 

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A Figura 2 abaixo mostra um aplicativo hipotético que usa o par regional para a recuperação de desastre. Os números verdes destacam as atividades entre regiões de três serviços do Azure (computação, armazenamento e banco de dados do Azure) e como eles são configurados para replicar entre regiões. Os benefícios exclusivos da implantação em regiões emparelhadas são realçados pelos números laranja.

![Visão geral dos benefícios da região emparelhada](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par regional do Azure hipotético

## <a name="cross-region-activities"></a>Atividades entre regiões
Conforme mencionado na Figura 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **computação do Azure (IaaS)** – você deve provisionar recursos de computação adicionais com antecedência para garantir que os recursos estejam disponíveis em outra região durante um desastre. Para obter mais informações, consulte [orientação técnica de resiliência do Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md).

![armazenamento](./media/best-practices-availability-paired-regions/2Green.png) **armazenamento do Azure** – se você estiver usando discos gerenciados, saiba mais sobre [backups entre regiões](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) com o backup do Azure e [replicando VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) de uma região para outra com Azure site Recovery. Se você estiver usando contas de armazenamento, o armazenamento com redundância geográfica (GRS) será configurado por padrão quando uma conta de armazenamento do Azure for criada. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para obter mais informações, consulte [Opções de redundância de armazenamento do Azure](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **banco de dados SQL do Azure** – com a replicação geográfica do banco de dados SQL do Azure, você pode configurar a replicação assíncrona de transações para qualquer região do mundo; no entanto, recomendamos que você implante esses recursos em uma região emparelhada para a maioria dos cenários de recuperação de desastres. Para obter mais informações, consulte [replicação geográfica no banco de dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md).

![o Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** -Resource Manager fornece inerentemente isolamento lógico de componentes entre regiões. Isso significa que as falhas lógicas em uma região têm menor probabilidade de afetar outra.

## <a name="benefits-of-paired-regions"></a>Benefícios das regiões emparelhadas
Conforme mencionado na Figura 2.  

Isolamento de ![](./media/best-practices-availability-paired-regions/5Orange.png)
**isolamento físico** – quando possível, o Azure prefere pelo menos 300 milhas de separação entre data centers em um par regional, embora isso não seja prático ou possível em todas as regiões geográficas. A separação do datacenter físico reduz a probabilidade de desastres naturais, danos civis, interrupções de energia ou interrupções de rede física que afetam as duas regiões ao mesmo tempo. O isolamento está sujeito às restrições na geografia (tamanho da geografia, disponibilidade da infraestrutura de energia/rede, regulamentos, etc.).  

replicação do ![](./media/best-practices-availability-paired-regions/6Orange.png)
**replicação fornecida pela plataforma** -alguns serviços, como o armazenamento com redundância geográfica, fornecem replicação automática para a região emparelhada.

![recuperação](./media/best-practices-availability-paired-regions/7Orange.png)
**ordem de recuperação de região** – no caso de uma interrupção ampla, a recuperação de uma região é priorizada de cada par. Os aplicativos implantados em regiões emparelhadas têm a garantia de ter uma das regiões recuperadas com prioridade. Se um aplicativo for implantado entre regiões que não são emparelhadas, a recuperação poderá ser atrasada – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![atualizações](./media/best-practices-availability-paired-regions/8Orange.png)
**atualizações sequenciais** – as atualizações planejadas do sistema do Azure são distribuídas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de bugs e as falhas lógicas no caso raro de uma atualização inválida.

Dados de ![](./media/best-practices-availability-paired-regions/9Orange.png)
**residência de dados** – uma região reside na mesma geografia que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de impostos e autoridades.
